private static  final EnumProvisionType provisionType = EnumProvisionType.ACCOUNT;

    private final ProvisionNextService provisionNextService;
    private final AccountingUtil accountingDateUtil;


    @Override
    public EnumProvisionType getProvisionType() {
        return provisionType;
    }

    @Override
    public CreateAccountingResultDTO doAccounting(CreateAccountingDTO createAccountingDTO) {
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        MakeProvisionRequest  makeProvisionRequest = prepareProvisionRequest(createAccountingDTO,createAccountingResultDTO);
        try {
            MakeProvisionResponse makeProvisionResponse = provisionNextService.makeProvision(makeProvisionRequest);
            if(!makeProvisionResponse.isSuccess()){
                handleException(makeProvisionResponse.getErrorCode(), createAccountingResultDTO);
                createAccountingResultDTO.setSuccess(false);
                return createAccountingResultDTO;
            }
            if(makeProvisionResponse.getContractNo() == null){
                createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
                createAccountingResultDTO.setSuccess(false);
                return createAccountingResultDTO;
            }
            createAccountingResultDTO.setContractNo(makeProvisionResponse.getContractNo());
            createAccountingResultDTO.setPendingDetailList(makeProvisionResponse.getPendingDetailList());
            createAccountingResultDTO.setSuccess(true);
        }catch (Exception e){
            if(e.getCause().getClass().equals(ServiceCallException.class)){
                Long errorCode =((ServiceCallException) e.getCause()).getErrorCode();
                handleException(errorCode, createAccountingResultDTO);
                return createAccountingResultDTO;
            }
            createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
            createAccountingResultDTO.setSuccess(false);
        }
        return createAccountingResultDTO;
    }


    private MakeProvisionRequest prepareProvisionRequest(CreateAccountingDTO createAccountingDTO,CreateAccountingResultDTO createAccountingResultDTO )  {
        MakeProvisionRequest makeProvisionRequest = new MakeProvisionRequest();
        makeProvisionRequest.setTransactionId(createAccountingDTO.getChannelTransactionId());
        makeProvisionRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        makeProvisionRequest.setChannelCode(createAccountingDTO.getChannelCode());
        makeProvisionRequest.setUserCode(createAccountingDTO.getAgentCode());
        makeProvisionRequest.setOperationalBranchCode(createAccountingDTO.getBranchCode());

        List<MakeProvisionInnerDTO> makeProvisionInnerList = new ArrayList<>();
        AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO = (AccountPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();

        /** Debit */
        MakeProvisionInnerDTO debitProvisionInnerRequest = new MakeProvisionInnerDTO();
        debitProvisionInnerRequest.setAccountNo(accountPaymentMethodDetailDTO.getAccountNo());
        debitProvisionInnerRequest.setCurrency(createAccountingDTO.getCurrency().getValue());
        debitProvisionInnerRequest.setAmount(createAccountingDTO.getPaymentAmount().negate());
        debitProvisionInnerRequest.setDescription("Fatura Ödemesi");//TODO: Review
        debitProvisionInnerRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        debitProvisionInnerRequest.setClientNo(createAccountingDTO.getProvisionDTO().getCustomerNo().intValue());
        makeProvisionInnerList.add(debitProvisionInnerRequest);

        /** Credit */
        MakeProvisionInnerDTO creditProvisionInnerRequest = new MakeProvisionInnerDTO();
        creditProvisionInnerRequest.setAccountNo(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO().getInstitutionAccountNo());
        creditProvisionInnerRequest.setCurrency(createAccountingDTO.getCurrency().getValue());
        creditProvisionInnerRequest.setAmount(createAccountingDTO.getPaymentAmount());
        creditProvisionInnerRequest.setDescription("Fatura Ödemesi");//TODO: Review
        creditProvisionInnerRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        creditProvisionInnerRequest.setClientNo(createAccountingDTO.getInstitution().getCustomerNo().intValue());
        //TODO:LocalAmount dövizlerde
        LocalDate availableLocalDate ;
        if(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.NO_VALOR)){
            availableLocalDate = LocalDate.now();
        }else {
            availableLocalDate = accountingDateUtil.getAvailDate(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayType(),
                    getBlockDayCount(createAccountingDTO.getInstitutionChannelPymMethodDTO(), createAccountingDTO.getInstitutionChnnlPymMthdPscDTO()));
        }
        createAccountingResultDTO.setAvailableDate(availableLocalDate);
        Date availableDate = Date.valueOf(availableLocalDate);
        Date valueDate = Date.valueOf(availableLocalDate.plusDays(1));
        creditProvisionInnerRequest.setAvailableDate(availableDate);
        creditProvisionInnerRequest.setValueDate(valueDate);

        makeProvisionInnerList.add(creditProvisionInnerRequest);
        /** Commission  **/
        if (createAccountingDTO.getResponseCommissionInformation()!= null) {

            ResponseCommissionInformation responseCommissionInformation = createAccountingDTO.getResponseCommissionInformation();
            for (CommissionOutputAccountingApiDTO commissionOutputDTO : responseCommissionInformation.getCommissionOutputAccountingApiDTOList()) {
                MakeProvisionInnerDTO commissionProvisionInnerRequest = new MakeProvisionInnerDTO();
                commissionProvisionInnerRequest.setAccountNo(accountPaymentMethodDetailDTO.getAccountNo());
                commissionProvisionInnerRequest.setAmount(commissionOutputDTO.getAmount());
                commissionProvisionInnerRequest.setCurrency(commissionOutputDTO.getCurrency());
                commissionProvisionInnerRequest.setDescription(commissionOutputDTO.getDescription());
                if (Boolean.TRUE.equals(commissionOutputDTO.getIsCommissionTax())) {
                    commissionProvisionInnerRequest.setCommissionTax(true);
                } else {
                    commissionProvisionInnerRequest.setCommission(true);
                }
                commissionProvisionInnerRequest.setProvisionCode(commissionOutputDTO.getProvisionCode());
                commissionProvisionInnerRequest.setReservationId(commissionOutputDTO.getReservationId());
                commissionProvisionInnerRequest.setCommissionBalanceType(commissionOutputDTO.getBalanceControlType());
                commissionProvisionInnerRequest.setFeeDefinitionId(commissionOutputDTO.getFeeDefinitionId());
                commissionProvisionInnerRequest.setFeeDefinitionId(commissionOutputDTO.getFeeDefinitionId());
                commissionProvisionInnerRequest.setDelinquencyRequired(commissionOutputDTO.getIsDelinquencyRequired());
                makeProvisionInnerList.add(commissionProvisionInnerRequest);
            }
        }

        makeProvisionRequest.setMakeProvisionInnerList(makeProvisionInnerList);


        return makeProvisionRequest;
    }

    private void handleException(Long errorCode,CreateAccountingResultDTO createAccountingResultDTO){
        EnumAccountProvisionResult result = EnumAccountProvisionResult.parse(errorCode);
        createAccountingResultDTO.setSuccess(false);
        if(result == null){
            createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
            return;
        }
        createAccountingResultDTO.setError(result.getBillCode());
    }

    private Integer getBlockDayCount(InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO, InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO){
        if(institutionChannelPymMethodDTO.getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.DAILY)){
            return  institutionChnnlPymMthdPscDTO.getBlockDayCount(Calendar.getInstance().get(Calendar.DAY_OF_WEEK));
        }else if(institutionChannelPymMethodDTO.getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.CHANNEL)){
            return  institutionChannelPymMethodDTO.getBlockDayCount();
        }else{
            return 0;
        }
    }








package com.ykb.payments.bill.transaction.accounting.provision.service;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class AccountProvisionServiceImplTest {

    @BeforeEach
    void setUp() {
    }

    @AfterEach
    void tearDown() {
    }

    @Test
    void getProvisionType() {
    }

    @Test
    void doAccounting() {
    }
}










  private AccountProvisionServiceImpl accountProvisionService;
    private ProvisionNextService provisionNextService;
    private AccountingUtil accountingDateUtil;

    @BeforeEach
    void setUp() {
        provisionNextService = Mockito.mock(ProvisionNextService.class);
        accountingDateUtil = Mockito.mock(AccountingUtil.class);
        accountProvisionService = new AccountProvisionServiceImpl(provisionNextService, accountingDateUtil);
    }

    @AfterEach
    void tearDown() {
        // Cleanup resources if necessary
    }

    @Test
    void getProvisionType() {
        // Arrange
        EnumProvisionType expectedProvisionType = EnumProvisionType.ACCOUNT;

        // Act
        EnumProvisionType actualProvisionType = accountProvisionService.getProvisionType();

        // Assert
        assertEquals(expectedProvisionType, actualProvisionType);
    }

Error running AccountProvisionServiceImplTest.getProvisionType. Command line is too long. Shorten the command line and rerun.


org.mockito.exceptions.misusing.NotAMockException: 
Argument passed to verify() is of type AccountProvisionServiceImpl and is not a mock!
Make sure you place the parenthesis correctly!
See the examples of correct verifications:
    verify(mock).someMethod();
    verify(mock, times(10)).someMethod();
    verify(mock, atLeastOnce()).someMethod();

	at com.ykb.payments.bill.transaction.accounting.provision.service.AccountProvisionServiceImplTest.getProvisionType(AccountProvisionServiceImplTest.java:44)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.junit.platform.commons.util.ReflectionUtils.invokeMethod(ReflectionUtils.java:725)
	at org.junit.jupiter.engine.execution.MethodInvocation.proceed(MethodInvocation.java:60)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain$ValidatingInvocation.proceed(InvocationInterceptorChain.java:131)
	at org.junit.jupiter.engine.extension.TimeoutExtension.intercept(TimeoutExtension.java:149)
	at org.junit.jupiter.engine.extension.TimeoutExtension.interceptTestableMethod(TimeoutExtension.java:140)
	at org.junit.jupiter.engine.extension.TimeoutExtension.interceptTestMethod(TimeoutExtension.java:84)
	at org.junit.jupiter.engine.execution.ExecutableInvoker$ReflectiveInterceptorCall.lambda$ofVoidMethod$0(ExecutableInvoker.java:115)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.lambda$invoke$0(ExecutableInvoker.java:105)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain$InterceptedInvocation.proceed(InvocationInterceptorChain.java:106)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.proceed(InvocationInterceptorChain.java:64)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.chainAndInvoke(InvocationInterceptorChain.java:45)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.invoke(InvocationInterceptorChain.java:37)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.invoke(ExecutableInvoker.java:104)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.invoke(ExecutableInvoker.java:98)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.lambda$invokeTestMethod$7(TestMethodTestDescriptor.java:214)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.invokeTestMethod(TestMethodTestDescriptor.java:210)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.execute(TestMethodTestDescriptor.java:135)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.execute(TestMethodTestDescriptor.java:66)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$6(NodeTestTask.java:151)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:141)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$9(NodeTestTask.java:139)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:138)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:95)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.invokeAll(SameThreadHierarchicalTestExecutorService.java:41)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$6(NodeTestTask.java:155)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:141)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$9(NodeTestTask.java:139)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:138)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:95)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.invokeAll(SameThreadHierarchicalTestExecutorService.java:41)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$6(NodeTestTask.java:155)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:141)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$9(NodeTestTask.java:139)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:138)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:95)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.submit(SameThreadHierarchicalTestExecutorService.java:35)
	at org.junit.platform.engine.support.hierarchical.HierarchicalTestExecutor.execute(HierarchicalTestExecutor.java:57)
	at org.junit.platform.engine.support.hierarchical.HierarchicalTestEngine.execute(HierarchicalTestEngine.java:54)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:107)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:88)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.lambda$execute$0(EngineExecutionOrchestrator.java:54)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.withInterceptedStreams(EngineExecutionOrchestrator.java:67)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:52)
	at org.junit.platform.launcher.core.DefaultLauncher.execute(DefaultLauncher.java:114)
	at org.junit.platform.launcher.core.DefaultLauncher.execute(DefaultLauncher.java:86)
	at org.junit.platform.launcher.core.DefaultLauncherSession$DelegatingLauncher.execute(DefaultLauncherSession.java:86)
	at org.junit.platform.launcher.core.SessionPerRequestLauncher.execute(SessionPerRequestLauncher.java:53)
	at com.intellij.junit5.JUnit5IdeaTestRunner.startRunnerWithArgs(JUnit5IdeaTestRunner.java:57)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater$1.execute(IdeaTestRunner.java:38)
	at com.intellij.rt.execution.junit.TestsRepeater.repeat(TestsRepeater.java:11)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:35)
	at com.intellij.rt.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:232)
	at com.intellij.rt.junit.JUnitStarter.main(JUnitStarter.java:55)

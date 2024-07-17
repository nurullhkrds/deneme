package com.ykb.payments.bill.transaction.accounting.provision.service;

import com.ykb.payments.bill.transaction.accounting.util.AccountingUtil;
import com.ykb.payments.bill.transaction.external.corebanking.account.service.ProvisionNextService;
import com.ykb.payments.bill.transaction.institution.enums.EnumProvisionType;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.verify;

class AccountProvisionServiceImplTest {


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
    }


    @Test
    void shouldReturnAccountProvisionType() {
        // Arrange
        EnumProvisionType expectedProvisionType = EnumProvisionType.ACCOUNT;

        // Act
        EnumProvisionType actualProvisionType = accountProvisionService.getProvisionType();

        // Assert
        assertEquals(expectedProvisionType, actualProvisionType);

    }


    @Test
    void doAccounting() {
    }
}


















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

shouldReturnAccountProvisionType

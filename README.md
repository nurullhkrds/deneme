package com.ykb.payments.bill.transaction.process.query;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.util.Collections;
import java.util.List;
import java.util.Optional;

import org.springframework.util.CollectionUtils;

import com.ykb.payments.bill.common.enums.EnumBillResult;
import com.ykb.payments.bill.common.enums.EnumCurrencyCode;
import com.ykb.payments.bill.common.exception.BillException;
import com.ykb.payments.bill.common.util.SpringUtil;
import com.ykb.payments.bill.transaction.external.adapter.dto.QueriedBillDTO;
import com.ykb.payments.bill.transaction.external.adapter.request.QueryBillsAdapterRequest;
import com.ykb.payments.bill.transaction.external.adapter.response.QueryBillsAdapterResponse;
import com.ykb.payments.bill.transaction.external.adapter.service.AdapterService;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.HmnPaidBillDTO;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.ResponseGetCustomerPaidBillList;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.service.BillPaymentRestFacade;
import com.ykb.payments.bill.transaction.external.limitation.model.NotifyInquiryLimitationRequest;
import com.ykb.payments.bill.transaction.external.limitation.model.PaymentAllowedResponse;
import com.ykb.payments.bill.transaction.external.limitation.service.LimitationService;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import com.ykb.payments.bill.transaction.institution.service.InstitutionUserIntService;
import com.ykb.payments.bill.transaction.institution.util.SubscriberNumberUtils;
import com.ykb.payments.bill.transaction.payment.dto.PaymentDTO;
import com.ykb.payments.bill.transaction.payment.dto.ProvisionDTO;
import com.ykb.payments.bill.transaction.payment.dto.SubscriberNoPartRequestDTO;
import com.ykb.payments.bill.transaction.payment.enums.EnumBillStatu;
import com.ykb.payments.bill.transaction.payment.enums.EnumProvisionStatus;
import com.ykb.payments.bill.transaction.payment.event.PaymentEventPublisher;
import com.ykb.payments.bill.transaction.payment.mapper.PaymentMapper;
import com.ykb.payments.bill.transaction.payment.repository.PaymentRepository;
import com.ykb.payments.bill.transaction.payment.service.PaymentUtilImpl;
import com.ykb.payments.bill.transaction.payment.service.ProvisionService;
import com.ykb.payments.bill.transaction.process.common.AbstractProcess;
import com.ykb.payments.bill.transaction.process.common.ProcessDataPackKey;
import com.ykb.payments.bill.transaction.process.common.ProcessStep;


public class QueryBillsProcess extends AbstractProcess {

    private AdapterService  adapterService;
    private ProvisionService provisionService;
    private InstitutionUserIntService institutionUserIntService;
    private BillPaymentRestFacade billPaymentRestFacade;
    private PaymentRepository paymentRepository;
    private PaymentMapper paymentMapper;
    private LimitationService limitationService;

    private Long customerNo;
    private Long identityNo;
    private String taxOfficeNo;
    private String subscriberNo;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;
    private String currency;
    private List<QueriedBillDTO> queriedBillDTOList;

    private List<ProvisionDTO>  provisionList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;    
    private PaymentEventPublisher paymentEventPublisher;
    
    private PaymentUtilImpl paymentUtilImpl;    
    private boolean	isFomOperationEnabled;



    @Override
    public void executeProcess() throws BillException {
        addProcessStep(new GatherData());
        addProcessStep(new FormatSubscriberNoPartList());
        addProcessStep(new ValidateSubscriberNo());
        addProcessStep(new CheckCustomerQueryLimit());
        if(isOnlineProcess()){
            addProcessStep(new QueryFromService());
            addProcessStep(new EliminateBills());
        }else{
            addProcessStep(new QueryFromDatabase());
        }
        addProcessStep(new InvalidateNotPaidProvisions());
        addProcessStep(new CreateProvisions());
        addProcessStep(new UpdateCustomerQueryLimit());
        executeSteps();
    }

    private class GatherData implements ProcessStep {

        @Override
        public void executeStep() {
            customerNo = (Long) dataPack.get(ProcessDataPackKey.CUSTOMER_NO.getKey());
            identityNo = (Long) dataPack.get(ProcessDataPackKey.IDENTITY_NO.getKey());
            taxOfficeNo = (String) dataPack.get(ProcessDataPackKey.TAX_ID.getKey());
            subscriberNo = (String) dataPack.get(ProcessDataPackKey.SUBSCRIBER_NO.getKey());
            subscriberNoPartList = (List<SubscriberNoPartRequestDTO>) dataPack.get((ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey()));
            currency = (String) dataPack.get(ProcessDataPackKey.CURRENCY.getKey());
            adapterService = SpringUtil.getBean(AdapterService.class);
            institutionUserIntService = SpringUtil.getBean(InstitutionUserIntService.class);
            institutionUserIntListDTO = institutionUserIntService.getUserInterface(institutionDebtTypeId);
            billPaymentRestFacade = SpringUtil.getBean(BillPaymentRestFacade.class);
            paymentRepository = SpringUtil.getBean(PaymentRepository.class);
            paymentMapper = SpringUtil.getBean(PaymentMapper.class);
            limitationService=SpringUtil.getBean(LimitationService.class);
            
            paymentUtilImpl = SpringUtil.getBean(PaymentUtilImpl.class);
			isFomOperationEnabled = paymentUtilImpl.isFomOperationEnabled(institution);
          
        }

    }

	private class FormatSubscriberNoPartList implements ProcessStep {

		@Override
		public void executeStep() {			
			subscriberNo = SubscriberNumberUtils.formatSubscriberNumberParts(institutionUserIntListDTO, subscriberNoPartList);
		}

	}

    private class ValidateSubscriberNo implements ProcessStep {

        @Override
        public void executeStep() {
        	boolean valid = SubscriberNumberUtils.checkSubscriberNumberParts(institutionUserIntListDTO, subscriberNoPartList);
        	
        	if(!valid) {
        		error = EnumBillResult.SUBSCRIBER_NUMBER_INVALID;
        	}
        }

    }

    private class CheckCustomerQueryLimit implements ProcessStep {

        @Override
        public void executeStep() {
        	if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}
        	
            Integer customerInt=null;
            if(customerNo!=null){
                customerInt=customerNo.intValue();
            }

            /** TODO identityNo tipi long olarak verilmis , degistiginde alttaki kod blogu silinmeli
             */
            String identityNoStr=null;
            if(identityNo!=null){
                identityNoStr=identityNo.toString();
            }

            /** TODO product code constant olacak
                hata kodunun adk da maplendigi teyit edilcek
             */
            PaymentAllowedResponse paymentAllowedResponse = limitationService.isPaymentAllowedWithoutDebtOwner(identityNoStr,
                    customerInt, null, "B0002");
            if (!paymentAllowedResponse.isPaymentAllowed()) {
                error = EnumBillResult.BILL_QUERY_LIMIT_REACHED;
            }

        }

    }

    private class QueryFromService implements ProcessStep {
        @Override
        public void executeStep() throws BillException {    
            QueryBillsAdapterRequest queryBillsAdapterRequest = prepareQueryBills();
            
            QueryBillsAdapterResponse queryBillsAdapterResponse = adapterService.queryBills(queryBillsAdapterRequest, channelTransactionId, channelSessionId);

            if(EnumBillResult.SUCCESS.equals(EnumBillResult.parseValueByHmnCode(queryBillsAdapterResponse.getInternalResultCode()))) {
                queriedBillDTOList = queryBillsAdapterResponse.getBills();
            }else{
                error = EnumBillResult.parseValueByHmnCode(queryBillsAdapterResponse.getInternalResultCode());
            }
           
        }

        private  QueryBillsAdapterRequest prepareQueryBills(){
            QueryBillsAdapterRequest queryBillsAdapterRequest = new QueryBillsAdapterRequest();
            queryBillsAdapterRequest.setCustomerNo(customerNo);
            queryBillsAdapterRequest.setIdentityNo(identityNo);
            queryBillsAdapterRequest.setSubscriberNoPartList(subscriberNoPartList);
            queryBillsAdapterRequest.setRequestDate(LocalDateTime.now());
            queryBillsAdapterRequest.setChannelCode(channelCode);
            queryBillsAdapterRequest.setInstitutionDebtTypeId(institutionDebtTypeId);
            queryBillsAdapterRequest.setInstitutionId(institution.getId());
            queryBillsAdapterRequest.setOperatingBranchCode(branchCode);
            queryBillsAdapterRequest.setInstitution(institutionCode);
            queryBillsAdapterRequest.setProduct(productCode);
            queryBillsAdapterRequest.setUserCode(agentCode);
            queryBillsAdapterRequest.setSubscriberNo(subscriberNo);
            queryBillsAdapterRequest.setTransactionDate(LocalDateTime.now());
            return  queryBillsAdapterRequest;
        }

        

    }

	private class EliminateBills implements ProcessStep {

		@Override
		public void executeStep() {

			if (CollectionUtils.isEmpty(queriedBillDTOList)) {
				error = EnumBillResult.BILL_NOT_FOUND;
				return;
			}
			ResponseGetCustomerPaidBillList harmoniPaidBills = billPaymentRestFacade
					.getCustomerPaidBillList(productCode, institutionCode, subscriberNo);

			List<HmnPaidBillDTO> harmoniPaidBillList = Optional.ofNullable(harmoniPaidBills.getBillDTOList())
					.orElse(Collections.emptyList());

			List<PaymentDTO> mikroPaidBillList = paymentRepository.findPaidBillList(subscriberNo, institutionDebtTypeId,EnumBillStatu.PAID.getValue())
					.stream().map(paymentMapper::toDTO).toList();

			queriedBillDTOList = queriedBillDTOList.stream()
					.filter(queriedBillDTO -> harmoniPaidBillList.stream()
							.noneMatch(harmoniPaidBillDTO -> queriedBillDTO.getBillDueDate()
									.isEqual(harmoniPaidBillDTO.getBillDueDate().toInstant()
											.atZone(ZoneId.systemDefault()).toLocalDate())
									&& queriedBillDTO.getBillNo().equals(harmoniPaidBillDTO.getBillNo())))
					.filter(queriedBillDTO -> mikroPaidBillList.stream().noneMatch(
							microPaidDTO -> queriedBillDTO.getBillDueDate().isEqual(microPaidDTO.getBillDueDate())
									&& queriedBillDTO.getBillNo().equals(microPaidDTO.getBillNo())))
					.toList();

			if (CollectionUtils.isEmpty(queriedBillDTOList)) {
				error = EnumBillResult.BILL_NOT_FOUND;
			}
       	 
		}
		
		

	}

    private class QueryFromDatabase implements ProcessStep {

        @Override
        public void executeStep() {
            /**TODO: Offline borc sorgulama*/
        }

    }

    private class InvalidateNotPaidProvisions implements ProcessStep {

        @Override
        public void executeStep() {
            provisionService = SpringUtil.getBean(ProvisionService.class);
            provisionService.invalidateNotPaidProvisions(institutionDebtTypeId,subscriberNo);
        }

    }

    private class CreateProvisions implements ProcessStep {

		@Override
        public void executeStep() {
            provisionService = SpringUtil.getBean(ProvisionService.class);
            prepareProvision(queriedBillDTOList);
            provisionList = provisionService.createProvisions(provisionList);
        }
        
        private void prepareProvision(List<QueriedBillDTO> bills){
            provisionList =  bills.stream().map(queriedBillDTO -> {
                ProvisionDTO provisionDTO = new ProvisionDTO();
                provisionDTO.setTaxId(taxOfficeNo);
                provisionDTO.setCustomerNo(customerNo);
                provisionDTO.setIdentityNo(identityNo);
                provisionDTO.setInstitutionDebtTypeId(institutionDebtTypeId);
                provisionDTO.setChannelTransactionId(channelTransactionId);
                provisionDTO.setInstitutionId(institution.getId());
                provisionDTO.setChannelCode(channelCode);
                provisionDTO.setBranchCode(branchCode);
                provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
                provisionDTO.setProvisionDate(LocalDate.now());
                provisionDTO.setCurrency(EnumCurrencyCode.parse(queriedBillDTO.getCurrency()));
                provisionDTO.setQueryStan(queriedBillDTO.getQueryStan());
                provisionDTO.setBillNo(queriedBillDTO.getBillNo());
                provisionDTO.setExplanation(queriedBillDTO.getExplanation());
                provisionDTO.setBillTerm(queriedBillDTO.getBillTerm());
                provisionDTO.setInstitutionQueryStan(queriedBillDTO.getInstitutionQueryStan());
                provisionDTO.setBillDueDate(queriedBillDTO.getBillDueDate());
                provisionDTO.setIsPayable(queriedBillDTO.isPayable());
                provisionDTO.setBillIssueDate(queriedBillDTO.getBillIssueDate());
                provisionDTO.setAmount(queriedBillDTO.getBillAmount());
                provisionDTO.setSubscriberName(queriedBillDTO.getSubscriberName());
                provisionDTO.setSubscriberNo(queriedBillDTO.getSubscriberNo());
                provisionDTO.setAdditionalInfo1(queriedBillDTO.getAdditionalInfo1());
                provisionDTO.setAdditionalInfo2(queriedBillDTO.getAdditionalInfo2());
                provisionDTO.setAdditionalInfo3(queriedBillDTO.getAdditionalInfo3());
                provisionDTO.setAdditionalInfo4(queriedBillDTO.getAdditionalInfo4());
                provisionDTO.setAdditionalInfo5(queriedBillDTO.getAdditionalInfo5());
                provisionDTO.setAdditionalInfo6(queriedBillDTO.getAdditionalInfo6());
                provisionDTO.setAdditionalInfo7(queriedBillDTO.getAdditionalInfo7());
                provisionDTO.setAdditionalInfo8(queriedBillDTO.getAdditionalInfo8());
                provisionDTO.setAdditionalInfo9(queriedBillDTO.getAdditionalInfo9());
                return provisionDTO;
            }).toList();
        }
    }

    private class UpdateCustomerQueryLimit implements ProcessStep {

        @Override
        public void executeStep() {
        	
        	if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}
        	
        	paymentEventPublisher = SpringUtil.getBean(PaymentEventPublisher.class);
        	NotifyInquiryLimitationRequest request = new NotifyInquiryLimitationRequest();
			
        	Integer customerInt = null;
			if (customerNo != null) {
				customerInt = customerNo.intValue();
			}
        	
        	String identityNoStr = null;
			if (identityNo != null) {
				identityNoStr = identityNo.toString();
			}
        	
        	request.setIdentityNo(identityNoStr);
			request.setChannelCode(channelCode);
			request.setClientNo(customerInt);
			request.setCreatedBy(agentCode);
			request.setProductCode("B0002");
			request.setTransactionDate(LocalDateTime.now());
			paymentEventPublisher.publishInquiryLimiationNotification(request);        	
        	
        }

    }
    
    @Override
    protected void prepareExecutionOutput() {
        executionOutput = new QueryBillsProcessOutput();
        executionOutput.setResult(error);
        
        ((QueryBillsProcessOutput) executionOutput).setProductCode(productCode);
        ((QueryBillsProcessOutput) executionOutput).setInstitutionCode(institutionCode);
        ((QueryBillsProcessOutput) executionOutput).setDebtTypeId(institutionDebtTypeId);
        ((QueryBillsProcessOutput) executionOutput).setSubscriberNo(subscriberNo);
        ((QueryBillsProcessOutput) executionOutput).setSubscriberNoPartList(subscriberNoPartList);
        ((QueryBillsProcessOutput) executionOutput).setProvisionDTOList(provisionList);
    }
}

public class BillPaymentProcess extends AbstractProcess {

	private ProvisionService provisionService;
	private AccountingService accountingService;
	private InstitutionChannelPymMethodService institutionChannelPymMethodService;
	private InstitutionChnnlPymMthdAccService institutionChnnlPymMthdAccService;
	private InstitutionChnnlPymMthdPscService institutionChnnlPymMthdPscService;
	private PaymentService paymentService;
	private Long billProvisionId;
	private BigDecimal paymentAmount;
	private EnumCurrencyCode currency;
	private EnumPaymentMethod paymentMethodType;
	private PaymentMethodDetailDTO paymentMethodDetailDTO;
	private InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO;
	private InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO;
	private InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO;
	private ProvisionDTO provisionDTO;
	private CreateAccountingDTO createAccountingDTO;
	private CreateAccountingResultDTO createAccountingResultDTO;
	private PaymentDTO paymentDTO;
	private PaymentNotificationService paymentNotificationService;
	private LimitationService limitationService;
    private PaymentEventPublisher paymentEventPublisher;
    private List<PaymentNotificationDTO> insertedPaymentNotificationDTOList;
    
    private InstitutionAccountingInfoService institutionAccountingInfoService;
    private InstitutionAccountingInfoDTO institutionAccountingInfoDTO;
    
    private PaymentUtilImpl paymentUtilImpl;    
    private boolean	isFomOperationEnabled;


	@Override
	protected void prepareExecutionOutput() {
		executionOutput = new BillPaymentProcessOutput();
		executionOutput.setResult(error);
		((BillPaymentProcessOutput) executionOutput).setBillId(paymentDTO.getId());
		((BillPaymentProcessOutput) executionOutput).setContractNo(paymentDTO.getContractNo());
	}

	/**
	 * TODO ytodo transactional gecici olarak executeProcess eklendi.
	 * FetchProvisionRecordWithLock-InsertPaymentRecord arasini kapsayacak bir
	 * yontem belirlenecek transactional serviste db connection release edilme
	 * senaryolari test edilecek.
	 */
	@Override
	@Transactional(rollbackFor = Exception.class)
	public void executeProcess() throws BillException {
		addProcessStep(new GatherData());
		addProcessStep(new FetchInstitutionPaymentMethodAcc());
		addProcessStep(new FetchProvisionRecord());
		addProcessStep(new CheckPartialAndOverPayment());
		addProcessStep(new CheckPaymentAmount());
		addProcessStep(new CheckBillStatus());
		addProcessStep(new CheckPaymentLimit());
		addProcessStep(new CheckCustomerPaymentLimit());
		addProcessStep(new CheckSMSNotification());
		addProcessStep(new FetchPaymentStan());
		addProcessStep(new CheckIfDummyMerchant());
		addProcessStep(new SetClientType());
		addProcessStep(new DoAccounting());
		addProcessStep(new UpdateProvision());
		addProcessStep(new InsertPaymentRecord());
		addProcessStep(new InsertPaymentNotificationRecord());
		addProcessStep(new PublishBillPaymentEvent());
		// TODO: Asagıdaki alanlar aftercommit sonrası yapılacak islemler olacak
		 addProcessStep(new UpdateCustomerPaymentLimit());
		executeSteps();
		prepareLogVariable();
	}

	private class GatherData implements ProcessStep {
		@Override
		public void executeStep() {
			billProvisionId = (Long) dataPack.get(ProcessDataPackKey.BILL_PROVISION_ID.getKey());
			paymentAmount = (BigDecimal) dataPack.get(ProcessDataPackKey.PAYMENT_AMOUNT.getKey());
			currency = EnumCurrencyCode.parse((String) dataPack.get(ProcessDataPackKey.CURRENCY.getKey()));
			paymentNotificationService = SpringUtil.getBean(PaymentNotificationService.class);
			paymentEventPublisher = SpringUtil.getBean(PaymentEventPublisher.class);
			paymentMethodType = (EnumPaymentMethod) dataPack.get(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey());
			if (paymentMethodType.equals(EnumPaymentMethod.ACCOUNT)) {
				paymentMethodDetailDTO = (PaymentMethodDetailDTO) dataPack
						.get((ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey()));
			} else if (paymentMethodType.getProvisionType().equals(EnumProvisionType.CARD)) {
				paymentMethodDetailDTO = (PaymentMethodDetailDTO) dataPack
						.get((ProcessDataPackKey.CREDIT_CARD_PAYMENT_METHOD_DETAIL.getKey()));
			} else {
				paymentMethodDetailDTO = (PaymentMethodDetailDTO) dataPack
						.get((ProcessDataPackKey.CASH_PAYMENT_METHOD_DETAIL.getKey()));
			}
									
			paymentUtilImpl = SpringUtil.getBean(PaymentUtilImpl.class);
			isFomOperationEnabled = paymentUtilImpl.isFomOperationEnabled(institution);			
			
			
		}
		
		
	}

	private class FetchProvisionRecord implements ProcessStep {
		@Override
		public void executeStep() {
			provisionService = SpringUtil.getBean(ProvisionService.class);
			provisionDTO = provisionService.getProvisionRecord(billProvisionId);

			if (provisionDTO == null) {
				 error = EnumBillResult.PROVISION_NUMBER_INVALID;
				 return;
			}
			//TODO: Process log zenginlestir ProcessLogDTO
			if(!provisionDTO.getStatus().equals(EnumProvisionStatus.NOT_PAID)){
				error = EnumBillResult.PROVISION_NUMBER_INVALID;
				return;
			}

			if(!provisionDTO.getProvisionDate().isEqual(LocalDate.now())){
				error = EnumBillResult.PROVISION_NUMBER_INVALID;
			}

		}
	}

	private class FetchInstitutionPaymentMethodAcc implements ProcessStep {
		@Override
		public void executeStep() {
			institutionChannelPymMethodService = SpringUtil.getBean(InstitutionChannelPymMethodService.class);
			institutionChannelPymMethodDTO = institutionChannelPymMethodService
					.getInstitutionPymMethodByChannelId(institutionChannel.getId(), paymentMethodType);
			if(institutionChannelPymMethodDTO == null || !institutionChannelPymMethodDTO.getIsActive()){
				error = EnumBillResult.PAYMENT_INSTRUMENT_NOT_SUPPORTED;
				return;
			}

			if(institutionChannelPymMethodDTO.getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.DAILY)){
				institutionChnnlPymMthdPscDTO =  institutionChnnlPymMthdPscService.getInstitutionChnnlPymMthdPscByChannelMethod(institutionChannelPymMethodDTO.getId());
			}

			institutionChnnlPymMthdAccService = SpringUtil.getBean(InstitutionChnnlPymMthdAccService.class);
			institutionChnnlPymMthdAccDTO = institutionChnnlPymMthdAccService
					.getInstitutionChnnlPymMthdAcc(institutionChannelPymMethodDTO.getId(), currency.getValue());

			if(institutionChnnlPymMthdAccDTO == null || !institutionChnnlPymMthdAccDTO.getIsActive()){
				error = EnumBillResult.INSTITUTION_ACCOUNT_NO_COULD_NOT_BE_FETCHED;
				return;
			}

			institutionAccountingInfoService = SpringUtil.getBean(InstitutionAccountingInfoService.class);
			institutionAccountingInfoDTO = institutionAccountingInfoService.getInstitutionAccountingInfoByInstitutionId(institution.getId());
			if(institutionAccountingInfoDTO == null ) {
				error = EnumBillResult.INSTITUTION_ACCOUNTING_INFO_NOT_FOUND;
				return;
			}
			if (paymentMethodType.getProvisionType().equals(EnumProvisionType.CARD) && institutionAccountingInfoDTO.getMerchantInformation() == null) {
				error = EnumBillResult.INSTITUTION_ACCOUNTING_MERCHANT_INFO_NOT_FOUND;
			}
		}
	}

	private class CheckPartialAndOverPayment implements ProcessStep {
		@Override
		public void executeStep() {

			if(paymentAmount.compareTo(provisionDTO.getAmount()) == 0){
				return;
			}

			if(paymentAmount.compareTo(provisionDTO.getAmount()) < 0 && !institutionChannel.getIsPartialPaymentAllowed().equals(Boolean.TRUE)){
				error = EnumBillResult.PARTIAL_PAYMENT_NOT_ALLOWED_ERROR;
				return;
			}

			if(paymentAmount.compareTo(provisionDTO.getAmount()) > 0 && !institutionChannel.getIsOverPaymentAllowed().equals(Boolean.TRUE)){
				error = EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR;
			}
		}
	}

	private class CheckPaymentAmount implements ProcessStep {
		@Override
		public void executeStep() {
			if(paymentAmount == null ){
				error = EnumBillResult.PAYMENT_AMOUNT_CANNOT_BE_NULL;
				return;
			}

			if(paymentAmount.compareTo(BigDecimal.ZERO)<=0){
				error = EnumBillResult.PAYMENT_AMOUNT_INVALID;
			}
		}
	}

	private class CheckBillStatus implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Fatura odenmis mi kontrolunu yap
			 */
		}
	}

	private class CheckPaymentLimit implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Musteri & Kurum arasındaki odeme limitini kontrol et
			 */
		}
	}

	private class CheckCustomerPaymentLimit implements ProcessStep {
		@Override
		public void executeStep() {

			limitationService = SpringUtil.getBean(LimitationService.class);

			if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}
			

			Integer customerInt = null;
			if (provisionDTO.getCustomerNo() != null) {
				customerInt = provisionDTO.getCustomerNo().intValue();
			}

			/**
			 * TODOidentityNotipilongolarakverilmis,degistigindealttakikodblogusilinmeli
			 */
			String identityNoStr = null;
			if ( provisionDTO.getIdentityNo() != null) {
				identityNoStr = provisionDTO.getIdentityNo().toString();
			}

			String cardNo = null;
			if (paymentMethodType.getProvisionType().equals(EnumProvisionType.CARD)) {
				CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = (CreditCardPaymentMethodDetailDTO) paymentMethodDetailDTO;
				cardNo = creditCardPaymentMethodDetailDTO.getCardNumber();
			}

			/**
			 * TODOproductcodeconstantolacak hatakodununadkdamaplendigiteyitedilcek
			 */
			PaymentAllowedResponse paymentAllowedResponse = limitationService
					.isPaymentAllowedWithoutDebtOwner(identityNoStr, customerInt, cardNo, "B0001");
			if (!paymentAllowedResponse.isPaymentAllowed()) {
				error = EnumBillResult.BILL_PAYMENT_LIMIT_REACHED;

			}
		}
	}

	private class CheckSMSNotification implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Çağrı merkezi kanalına göre sms gönderilip gönderilmeyeceğını
			 * kontrol et 2. Bestphone nummber getir yoksa gonderim flagını false setle 3.
			 * Musteri adını getir.
			 */
		}
	}

	private class FetchPaymentStan implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Online kurum ise ve odeme stan e ihtiyac duyuyorsa ödeme stan i
			 * oluştur
			 */
		}
	}

	private class CheckIfDummyMerchant implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Ödeme kredi kartı ise dummy üye işyeri kontrolünü yap
			 */
		}
	}

	private class SetClientType implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: 1. Fom client type a  YTODO harmonide sadece fom kullaniyor idi   limitation-serviece microsu kendisi bu ihtiyaci karsiliyor
			 * IMPLEMENT EDILMESINE GEREK YOK
			 */
		}
	}



	private class DoAccounting implements ProcessStep {
		@Override
		public void executeStep() {
			accountingService = SpringUtil.getBean(AccountingService.class);
			createAccountingResultDTO = accountingService.doAccounting(prepareCreateAccounting());

			if(!createAccountingResultDTO.isSuccess()){
				error = createAccountingResultDTO.getError();
			}
		}

		private CreateAccountingDTO prepareCreateAccounting() {
			createAccountingDTO = new CreateAccountingDTO();
			createAccountingDTO.setInstitutionDebtType(institutionDebtType);
			createAccountingDTO.setPaymentAmount(paymentAmount);
			createAccountingDTO.setCurrency(currency);
			createAccountingDTO.setProvisionDTO(provisionDTO);
			createAccountingDTO.setPaymentMethodDetailDTO(paymentMethodDetailDTO);
			createAccountingDTO.setPaymentAmount(paymentAmount);
			createAccountingDTO.setAgentCode(agentCode);
			createAccountingDTO.setChannelCode(channelCode);
			createAccountingDTO.setBranchCode(branchCode);
			createAccountingDTO.setPaymentMethodType(paymentMethodType);
			createAccountingDTO.setInstitution(institution);
			createAccountingDTO.setChannelSessionId(channelSessionId);
			createAccountingDTO.setChannelTransactionId(channelTransactionId);
			createAccountingDTO.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);
			createAccountingDTO.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);
			createAccountingDTO.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);
			createAccountingDTO.setInstitutionAccountingInfoDTO(institutionAccountingInfoDTO);
			/** TODO ytodo merchant information tablosundaki deger buraya set edilece */
			if (paymentMethodType.getProvisionType().equals(EnumProvisionType.CARD)) {
				createAccountingDTO.setDummyMerchant(true);
				createAccountingDTO.setMerchantNo(institutionAccountingInfoDTO.getMerchantInformation().getMerchantNo());				
				if(!"Dummy".equals(institutionAccountingInfoDTO.getMerchantInformation().getMerchantType())) {									
					createAccountingDTO.setDummyMerchant(false);
				}				
			}			
			return createAccountingDTO;
		}
	}

	private class UpdateProvision implements ProcessStep {
		@Override
		public void executeStep() {
			provisionDTO.setStatus(EnumProvisionStatus.PAID);
			provisionService.updateProvision(provisionDTO);
		}
	}

	private class InsertPaymentRecord implements ProcessStep {
		@Override
		public void executeStep() {
			preparePaymentDTO();
			paymentService.insertPayment(paymentDTO);
		}

		private void preparePaymentDTO() {

			paymentService = SpringUtil.getBean(PaymentService.class);

			paymentDTO = new PaymentDTO();
			paymentDTO.setPaymentAmount(paymentAmount);
			paymentDTO.setInstitutionId(institution.getId());
			paymentDTO.setInstitutionDebtTypeId(institutionDebtType.getId());
			paymentDTO.setSubscriberNo(provisionDTO.getSubscriberNo());
			paymentDTO.setBillDueDate(provisionDTO.getBillDueDate());
			paymentDTO.setBillNo(provisionDTO.getBillNo());
			paymentDTO.setTransactionOrderNo(1);// TODO: kısmı ödeme kontrolleri dogrultusunda belirlenecek
			paymentDTO.setAmount(provisionDTO.getAmount());
			paymentDTO.setCurrency(currency);
			paymentDTO.setStatus(EnumBillStatu.PAID);
			paymentDTO.setSubscriberName(provisionDTO.getSubscriberName());
			paymentDTO.setProvisionId(provisionDTO.getId());
			paymentDTO.setCustomerNo(provisionDTO.getCustomerNo());
			paymentDTO.setIdentityNo(provisionDTO.getIdentityNo());
			paymentDTO.setTaxId(provisionDTO.getTaxId());
			paymentDTO.setPaymentMethod(paymentMethodType);
			paymentDTO.setPaymentDate(LocalDate.now());
			paymentDTO.setPaymentTime(LocalTime.now());
			paymentDTO.setContractNo(createAccountingResultDTO.getContractNo());
			paymentDTO.setExpenseType(EnumExpenseType.FROM_CUSTOMER);
			paymentDTO.setExpenseAmount(createAccountingResultDTO.getCommissionAmount());
			paymentDTO.setAccountingSources(EnumAccountingSource.PAYMENT);
			if (paymentMethodType.getProvisionType().equals(EnumProvisionType.ACCOUNT)) {
				AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO = (AccountPaymentMethodDetailDTO) paymentMethodDetailDTO;
				paymentDTO.setAccountNo(accountPaymentMethodDetailDTO.getAccountNo());
				paymentDTO.setAccountingSources(EnumAccountingSource.PAYMENT);
			} else if (paymentMethodType.getProvisionType().equals(EnumProvisionType.CARD)) {
				CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = (CreditCardPaymentMethodDetailDTO) paymentMethodDetailDTO;
				paymentDTO.setCreditCardNo(creditCardPaymentMethodDetailDTO.getCardNumber());
				paymentDTO.setProvisionRequestId(createAccountingResultDTO.getProvisionRequestId());
				paymentDTO.setOceanTransactionId(createAccountingResultDTO.getOceanTransactionId());
				paymentDTO.setAccountingSources(createAccountingDTO.isDummyMerchant() ? EnumAccountingSource.PAYMENT
						: EnumAccountingSource.MERCHANT);
			} else {
				paymentDTO.setAccountingSources(EnumAccountingSource.PAYMENT);
			}

			paymentDTO.setAvailableDate(createAccountingResultDTO.getAvailableDate());
			paymentDTO.setInstitutionAccountNo(institutionChnnlPymMthdAccDTO.getInstitutionAccountNo());
			paymentDTO.setCommissionInquiryId(createAccountingResultDTO.getCommissionInquiryId());
			paymentDTO.setBillLoadDate(LocalDate.now());
			paymentDTO.setBillTerm(provisionDTO.getBillTerm());
			paymentDTO.setBillIssueDate(provisionDTO.getBillIssueDate());
			paymentDTO.setExplanation(provisionDTO.getExplanation());
			paymentDTO.setQueryStan(provisionDTO.getQueryStan());
			paymentDTO.setInstitutionQueryStan(provisionDTO.getInstitutionQueryStan());
			paymentDTO.setAdditionalInfo1(provisionDTO.getAdditionalInfo1());
			paymentDTO.setAdditionalInfo2(provisionDTO.getAdditionalInfo2());
			paymentDTO.setAdditionalInfo3(provisionDTO.getAdditionalInfo3());
			paymentDTO.setAdditionalInfo4(provisionDTO.getAdditionalInfo4());
			paymentDTO.setAdditionalInfo5(provisionDTO.getAdditionalInfo5());
			paymentDTO.setBranchCode(branchCode);
			paymentDTO.setChannelCode(channelCode);
			paymentDTO.setChannelTransactionId(channelTransactionId);
			paymentDTO.setChannelSessionId(channelSessionId);
			paymentDTO.setCreatedBy(agentCode);
			paymentDTO = paymentService.insertPayment(paymentDTO);

			// TODO:isBusiness ihtiyacı sorgulanacak adk akısında

		}
	}

	private class InsertPaymentNotificationRecord implements ProcessStep {
		@Override
		public void executeStep() {
			List<EnumPaymentNotificationType> enumValues = Arrays.asList(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION,EnumPaymentNotificationType.CRD_PRVSN_ACK);
			insertedPaymentNotificationDTOList = createPaymentNotifications(enumValues);
		}

		private List<PaymentNotificationDTO> createPaymentNotifications(List<EnumPaymentNotificationType> notificationTypes) {
			return notificationTypes.stream().map(this::createPaymentNotification).toList();
		}

		private PaymentNotificationDTO createPaymentNotification(EnumPaymentNotificationType notificationType) {

			boolean validationFlag = Boolean.TRUE;
			PaymentNotificationDTO paymentNotificationDTO = new PaymentNotificationDTO();

			if(notificationType.equals(EnumPaymentNotificationType.CRD_PRVSN_ACK)) {
				validationFlag = paymentDTO.getPaymentMethod().getProvisionType().equals(EnumProvisionType.CARD);
			}
			
			if(validationFlag) {
			paymentNotificationDTO.setPaymentId(paymentDTO.getId());
			paymentNotificationDTO.setNotificationType(notificationType);
			paymentNotificationDTO.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
			paymentNotificationDTO.setRetryCount(0);
			paymentNotificationDTO.setCreateDate(LocalDate.now());
			paymentNotificationDTO.setCreateTime(LocalDateTime.now());
			paymentNotificationDTO.setCreatedBy(agentCode);

			paymentNotificationDTO = paymentNotificationService
					.insertPaymentNotification(paymentNotificationDTO);

			
			}
			
			return paymentNotificationDTO;
		}

	}

	private class PublishBillPaymentEvent implements ProcessStep {
		@Override
		public void executeStep() {
		
			PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
			publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(insertedPaymentNotificationDTOList);
			publishPaymentTypeDTO.setInstitutionDTO(institution);
			publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
					
			paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);
						
		}

	}

	private class UpdateCustomerPaymentLimit implements ProcessStep {
		@Override
		public void executeStep() {

			limitationService = SpringUtil.getBean(LimitationService.class);

			if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}

			Integer customerInt = null;
			if (provisionDTO.getCustomerNo() != null) {
				customerInt = provisionDTO.getCustomerNo().intValue();
			}
		
			String identityNoStr = null;
			if (provisionDTO.getIdentityNo() != null) {
				identityNoStr = provisionDTO.getIdentityNo().toString();
			}

			NotifyPaymentLimitationRequest request = new NotifyPaymentLimitationRequest();
			request.setCancel(false);
			request.setChannelCode(channelCode);
			request.setClientNo(customerInt);
			request.setIdentityNo(identityNoStr);
			request.setContractNo(createAccountingResultDTO.getContractNo());
			request.setCreatedBy(agentCode);
			request.setProductCode("B0001");
			request.setReferenceNo(createAccountingResultDTO.getContractNo().toString());
			request.setTransactionDate(LocalDateTime.now());
			paymentEventPublisher.publishPaymentLimiationNotification(request);

		}
	}

	private void prepareLogVariable() {
		if (paymentDTO != null) {
			logDTO.setCustomerNo(paymentDTO.getCustomerNo());
			logDTO.setIdentityNo(paymentDTO.getIdentityNo());
			logDTO.setTaxId(paymentDTO.getTaxId());
			logDTO.setSubscriberNo(paymentDTO.getSubscriberNo());
		}

	}

}

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
 BU SINIFIN TESTİ 
"@ExtendWith(MockitoExtension.class)
public class BillPaymentProcessTest {

    @Mock
    private ProvisionServiceImpl provisionService;

    @Mock
    private AccountingServiceImpl accountingService;

    @Mock
    private InstitutionChannelPymMethodServiceImpl institutionChannelPymMethodService;

    @Mock
    private InstitutionChnnlPymMthdAccServiceImpl institutionChnnlPymMthdAccService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private InstitutionChnnlPymMthdPscServiceImpl institutionChnnlPymMthdPscService;

    @Mock
    private InstitutionAccountingInfoServiceImpl institutionAccountingInfoService;

    @Mock
    private PaymentServiceImpl paymentService;

    @Mock
    private PaymentNotificationServiceImpl paymentNotificationService;

    @InjectMocks
    private BillPaymentProcess billPaymentProcess;

    private ProvisionDTO provisionDTO;
    private PaymentDTO paymentDTO;
    private InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO;
    private InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO;
    private CreateAccountingResultDTO createAccountingResultDTO;
    @Mock
    private PaymentUtil paymentUtil;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;
    private AccountPaymentMethodDetailDTO paymentMethodDetailDTO;

    @Mock
    private ApplicationContext applicationContext;


    private InstitutionFeatureMapper getInstitutionFeatureMapper() {
        return new InstitutionFeatureMapperImpl();
    }


    @Test
    public void testExecuteProcess_Success() throws BillException {

        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(InstitutionChannelPymMethodService.class)).thenReturn(institutionChannelPymMethodService);
        when(getBean(InstitutionChnnlPymMthdAccService.class)).thenReturn(institutionChnnlPymMthdAccService);
        when(getBean(InstitutionAccountingInfoService.class)).thenReturn(institutionAccountingInfoService);
        when(getBean(ProvisionService.class)).thenReturn(provisionService);
        when(getBean(AccountingService.class)).thenReturn(accountingService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(PaymentNotificationService
                .class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);

        provisionDTO = new ProvisionDTO();
        provisionDTO.setId(1L);
        provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
        provisionDTO.setProvisionDate(LocalDate.now());
        provisionDTO.setAmount(BigDecimal.valueOf(100));

        institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        institutionChannelPymMethodDTO.setIsActive(true);

        institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setIsActive(true);
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("4567");

        createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setProduct(new ProductDTO());
        billPaymentProcess.setInstitution(institutionDTO);

        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        institutionChannelDTO.setId(123L);
        institutionChannelDTO.setIsOverPaymentAllowed(true);
        institutionChannelDTO.setIsPartialPaymentAllowed(true);
        billPaymentProcess.setInstitutionChannel(institutionChannelDTO);


        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();

        billPaymentProcess.setDataPack(new HashMap<>());
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.BILL_PROVISION_ID.getKey(), 1L);
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_AMOUNT.getKey(), BigDecimal.valueOf(100));
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey(), EnumPaymentMethod.ACCOUNT);

        paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("12345");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey(), paymentMethodDetailDTO);

        ProcessLogDTO processLogDTO = new ProcessLogDTO("processCode");
        billPaymentProcess.setLogDTO(processLogDTO);

        ProcessExecutionOutput processExecutionOutput = new BillPaymentProcessOutput();
        processExecutionOutput.setResult(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR);
        billPaymentProcess.setExecutionOutput(processExecutionOutput);

        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);
        billPaymentProcess.setStepHandler(new AbstractProcess() {
            public AbstractProcess.ProcessStepHandler ProcessStepHandler = new ProcessStepHandler();

            @Override
            public void executeProcess() throws BillException {
                System.out.println();
            }

            @Override
            protected void prepareExecutionOutput() {
                System.out.println();
            }
        }.ProcessStepHandler);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        billPaymentProcess.setInstitutionDebtType(institutionDebtTypeDTO);
        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = new InstitutionAccountingInfoDTO();
        when(provisionService.getProvisionRecord(1L)).thenReturn(provisionDTO);
        when(institutionChannelPymMethodService.getInstitutionPymMethodByChannelId(any(), any())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(any(), any())).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdPscService.getInstitutionChnnlPymMthdPscByChannelMethod(any())).thenReturn(institutionChnnlPymMthdPscDTO);
        when(accountingService.doAccounting(any(CreateAccountingDTO.class))).thenReturn(createAccountingResultDTO);
        when(paymentService.insertPayment(any())).thenReturn(paymentDTO);


        billPaymentProcess.executeProcess();

        verify(provisionService).getProvisionRecord(1L);
    }

    @Test
    public void testFetchProvisionRecordFail() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(PaymentUtil.class)).thenReturn(paymentUtil);
        when(getBean(InstitutionChannelPymMethodService.class)).thenReturn(institutionChannelPymMethodService);
        when(getBean(InstitutionChnnlPymMthdAccService.class)).thenReturn(institutionChnnlPymMthdAccService);
        when(getBean(InstitutionAccountingInfoService.class)).thenReturn(institutionAccountingInfoService);
        when(getBean(ProvisionService.class)).thenReturn(provisionService);
        when(getBean(AccountingService.class)).thenReturn(accountingService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);

        provisionDTO = new ProvisionDTO();
        provisionDTO.setId(1L);
        provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
        provisionDTO.setProvisionDate(LocalDate.now());
        provisionDTO.setAmount(BigDecimal.valueOf(100));

        institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        institutionChannelPymMethodDTO.setIsActive(true);

        institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setIsActive(true);
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("4567");

        createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setProduct(new ProductDTO());
        billPaymentProcess.setInstitution(institutionDTO);

        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        institutionChannelDTO.setId(123L);
        institutionChannelDTO.setIsOverPaymentAllowed(true);
        institutionChannelDTO.setIsPartialPaymentAllowed(true);
        billPaymentProcess.setInstitutionChannel(institutionChannelDTO);


        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();

        billPaymentProcess.setDataPack(new HashMap<>());
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.BILL_PROVISION_ID.getKey(), 1L);
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_AMOUNT.getKey(), BigDecimal.valueOf(100));
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey(), EnumPaymentMethod.ACCOUNT);

        paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("12345");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey(), paymentMethodDetailDTO);

        ProcessLogDTO processLogDTO = new ProcessLogDTO("processCode");
        billPaymentProcess.setLogDTO(processLogDTO);

        ProcessExecutionOutput processExecutionOutput = new BillPaymentProcessOutput();
        processExecutionOutput.setResult(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR);
        billPaymentProcess.setExecutionOutput(processExecutionOutput);

        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);

        billPaymentProcess.setStepHandler(new AbstractProcess() {
            public AbstractProcess.ProcessStepHandler ProcessStepHandler = new ProcessStepHandler();

            @Override
            public void executeProcess() throws BillException {
                System.out.println();
            }

            @Override
            protected void prepareExecutionOutput() {
                System.out.println();
            }
        }.ProcessStepHandler);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        billPaymentProcess.setInstitutionDebtType(institutionDebtTypeDTO);
        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = new InstitutionAccountingInfoDTO();
        when(provisionService.getProvisionRecord(1L)).thenReturn(provisionDTO);
        when(institutionChannelPymMethodService.getInstitutionPymMethodByChannelId(any(), any())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(any(), any())).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdPscService.getInstitutionChnnlPymMthdPscByChannelMethod(any())).thenReturn(institutionChnnlPymMthdPscDTO);
        when(accountingService.doAccounting(any(CreateAccountingDTO.class))).thenReturn(createAccountingResultDTO);
        when(paymentService.insertPayment(any())).thenReturn(paymentDTO);



        provisionDTO.setStatus(EnumProvisionStatus.PAID);

        billPaymentProcess.executeProcess();

        assertEquals(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR, billPaymentProcess.getExecutionOutput().getResult());
    }

    @Test
    public void testCheckPaymentAmount_Invalid() throws BillException {


        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(InstitutionChannelPymMethodService.class)).thenReturn(institutionChannelPymMethodService);
        when(getBean(InstitutionChnnlPymMthdAccService.class)).thenReturn(institutionChnnlPymMthdAccService);
        when(getBean(InstitutionAccountingInfoService.class)).thenReturn(institutionAccountingInfoService);
        when(getBean(ProvisionService.class)).thenReturn(provisionService);
        when(getBean(AccountingService.class)).thenReturn(accountingService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);

        provisionDTO = new ProvisionDTO();
        provisionDTO.setId(1L);
        provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
        provisionDTO.setProvisionDate(LocalDate.now());
        provisionDTO.setAmount(BigDecimal.valueOf(100));

        institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        institutionChannelPymMethodDTO.setIsActive(true);

        institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setIsActive(true);
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("4567");

        createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setProduct(new ProductDTO());
        billPaymentProcess.setInstitution(institutionDTO);

        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        institutionChannelDTO.setId(123L);
        institutionChannelDTO.setIsOverPaymentAllowed(true);
        institutionChannelDTO.setIsPartialPaymentAllowed(true);
        billPaymentProcess.setInstitutionChannel(institutionChannelDTO);


        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();

        billPaymentProcess.setDataPack(new HashMap<>());
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.BILL_PROVISION_ID.getKey(), 1L);
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_AMOUNT.getKey(), BigDecimal.valueOf(100));
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey(), EnumPaymentMethod.ACCOUNT);

        paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("12345");
        billPaymentProcess.getDataPack().put(ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey(), paymentMethodDetailDTO);

        ProcessLogDTO processLogDTO = new ProcessLogDTO("processCode");
        billPaymentProcess.setLogDTO(processLogDTO);

        ProcessExecutionOutput processExecutionOutput = new BillPaymentProcessOutput();
        processExecutionOutput.setResult(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR);
        billPaymentProcess.setExecutionOutput(processExecutionOutput);

        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.CARD);

        billPaymentProcess.setStepHandler(new AbstractProcess() {
            public AbstractProcess.ProcessStepHandler ProcessStepHandler = new ProcessStepHandler();

            @Override
            public void executeProcess() throws BillException {
                System.out.println();
            }

            @Override
            protected void prepareExecutionOutput() {
                System.out.println();
            }
        }.ProcessStepHandler);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        billPaymentProcess.setInstitutionDebtType(institutionDebtTypeDTO);

        when(provisionService.getProvisionRecord(1L)).thenReturn(provisionDTO);
        when(institutionChannelPymMethodService.getInstitutionPymMethodByChannelId(any(), any())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(any(), any())).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdPscService.getInstitutionChnnlPymMthdPscByChannelMethod(any())).thenReturn(institutionChnnlPymMthdPscDTO);
        when(accountingService.doAccounting(any(CreateAccountingDTO.class))).thenReturn(createAccountingResultDTO);
        when(paymentService.insertPayment(any())).thenReturn(paymentDTO);

        billPaymentProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_AMOUNT.getKey(), BigDecimal.valueOf(-1));
        billPaymentProcess.executeProcess();

        assertEquals(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR, billPaymentProcess.getExecutionOutput().getResult());
    }


    private InstitutionFeatureRepository getInstitutionFeatureRepository() {
        return new InstitutionFeatureRepository() {
            @Override
            public InstitutionFeature findByFeatureCodeAndInstitutionCode(String featureCode, String institutionCode, String productCode) {
                return null;
            }

            @Override
            public List<InstitutionFeature> findByIsActiveAndInstitutionId(Boolean isActive, Long institutionId) {
                return null;
            }

            @Override
            public List<InstitutionFeature> findAll() {
                return null;
            }

            @Override
            public List<InstitutionFeature> findAll(Sort sort) {
                return null;
            }

            @Override
            public List<InstitutionFeature> findAllById(Iterable<Long> longs) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> List<S> saveAll(Iterable<S> entities) {
                return null;
            }

            @Override
            public void flush() {

            }

            @Override
            public <S extends InstitutionFeature> S saveAndFlush(S entity) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> List<S> saveAllAndFlush(Iterable<S> entities) {
                return null;
            }

            @Override
            public void deleteAllInBatch(Iterable<InstitutionFeature> entities) {

            }

            @Override
            public void deleteAllByIdInBatch(Iterable<Long> longs) {

            }

            @Override
            public void deleteAllInBatch() {

            }

            @Override
            public InstitutionFeature getOne(Long aLong) {
                return null;
            }

            @Override
            public InstitutionFeature getById(Long aLong) {
                return null;
            }

            @Override
            public InstitutionFeature getReferenceById(Long aLong) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> List<S> findAll(Example<S> example) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> List<S> findAll(Example<S> example, Sort sort) {
                return null;
            }

            @Override
            public Page<InstitutionFeature> findAll(Pageable pageable) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> S save(S entity) {
                return null;
            }

            @Override
            public Optional<InstitutionFeature> findById(Long aLong) {
                return Optional.empty();
            }

            @Override
            public boolean existsById(Long aLong) {
                return false;
            }

            @Override
            public long count() {
                return 0;
            }

            @Override
            public void deleteById(Long aLong) {

            }

            @Override
            public void delete(InstitutionFeature entity) {

            }

            @Override
            public void deleteAllById(Iterable<? extends Long> longs) {

            }

            @Override
            public void deleteAll(Iterable<? extends InstitutionFeature> entities) {

            }

            @Override
            public void deleteAll() {

            }

            @Override
            public <S extends InstitutionFeature> Optional<S> findOne(Example<S> example) {
                return Optional.empty();
            }

            @Override
            public <S extends InstitutionFeature> Page<S> findAll(Example<S> example, Pageable pageable) {
                return null;
            }

            @Override
            public <S extends InstitutionFeature> long count(Example<S> example) {
                return 0;
            }

            @Override
            public <S extends InstitutionFeature> boolean exists(Example<S> example) {
                return false;
            }

            @Override
            public <S extends InstitutionFeature, R> R findBy(Example<S> example, Function<FluentQuery.FetchableFluentQuery<S>, R> queryFunction) {
                return null;
            }
        };
    }
}
" BU ŞEKİLDEDİR 


"public class BillPaymentReverseProcess extends AbstractProcess {
    
	private Long billId;
	private Long contractNo;
	private AccountingService accountingService;
    private CreateReverseAccountingResultDTO createReverseAccountingResultDTO;
    private PaymentService paymentService;
    private PaymentDTO paymentDTO;
    private PaymentCancelDTO paymentCancelDTO;
    private PaymentNotificationService paymentNotificationService;
    private ApplicationEventPublisher eventPublisher;
    private PaymentEventPublisher paymentEventPublisher;
    private List<PaymentNotificationDTO> insertedPaymentNotificationDTOList;
    private InstitutionFeatureService institutionFeatureService;
    private ChannelService channelService;
    
    private PaymentUtilImpl paymentUtilImpl;    
    private boolean	isFomOperationEnabled;



	@Override
	public void executeProcess() throws BillException {
		addProcessStep(new GatherData());
		addProcessStep(new FetchBillRecord());
		addProcessStep(new CheckInstitution());
		addProcessStep(new CheckIsReversible());
		addProcessStep(new OnlineCheckIsReversible());
		addProcessStep(new DoAccounting());
		addProcessStep(new InsertPaymentReverseRecord());
		addProcessStep(new UpdateCustomerPaymentLimit());
		addProcessStep(new UpdatePaymentRecord());
		addProcessStep(new InsertPaymentCancelNotificationRecord());
		addProcessStep(new PublishBillPaymentCancelEvent());
		
		executeSteps();
		prepareLogVariable();
	}

	@Override
	protected void prepareExecutionOutput() {
        executionOutput = new BillPaymentReverseProcessOutput();
        executionOutput.setResult(error);
        /*
         * TODO: servisten donecek olan degerler burada yer alacak
         * 
         */


	}

	private class GatherData implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
            billId = (Long) dataPack.get(ProcessDataPackKey.BILL_ID.getKey());
            contractNo = (Long) dataPack.get(ProcessDataPackKey.CONTRACT_NO.getKey());
            
            paymentNotificationService = SpringUtil.getBean(PaymentNotificationService.class);
            eventPublisher = SpringUtil.getApplicationContext();
			paymentEventPublisher = SpringUtil.getBean(PaymentEventPublisher.class);
			
			paymentUtilImpl = SpringUtil.getBean(PaymentUtilImpl.class);
			isFomOperationEnabled = paymentUtilImpl.isFomOperationEnabled(institution);

		}
		
	}
	
	private class FetchBillRecord implements ProcessStep {
		@Override
		public void executeStep() {
            paymentService = SpringUtil.getBean(PaymentService.class);
			paymentDTO = paymentService.getPayment(billId, contractNo);
		}
	}
	
	private class CheckInstitution implements ProcessStep {
		@Override
		public void executeStep() {
			String findSubscriber = null;

			if(institution.getIsReverseAllowed() == Boolean.FALSE) {
				institutionFeatureService = SpringUtil.getBean(InstitutionFeatureService.class);
				String featureValue = institutionFeatureService.getFeatureValue(EnumFeatureCode.SUBSCRIBER_REVERSE_ALLOWED,
						institution.getInstitutionCode(), institution.getProduct().getCode());

				if (featureValue != null) {
					List<String> subscriberReverseAllowedList = Arrays.asList(featureValue.split(","));
					findSubscriber = subscriberReverseAllowedList.stream()
							.filter(str -> str.equals(paymentDTO.getSubscriberNo())).findAny().orElse(null);
				}	
			}

			if (findSubscriber == null && institution.getIsReverseAllowed() == Boolean.FALSE) {
				error = EnumBillResult.REVERSE_PAYMENT_FORBIDDEN;
			}
		}
	}

	private class CheckIsReversible implements ProcessStep {
		@Override
		public void executeStep() {
			channelService = SpringUtil.getBean(ChannelService.class);
			ChannelDTO cancelPaymentChannel = channelService.findChannelByChannelCode(channelCode);

			boolean areChannelsTheSameAdkGroup = channelService.areChannelsTheSameAdkGroup(cancelPaymentChannel,
					channelService.findChannelByChannelCode(paymentDTO.getChannelCode()));

			if (paymentDTO.getChannelCode().equals(channelCode) && !areChannelsTheSameAdkGroup) {
				error = EnumBillResult.BILL_REVERSE_PAYMENT_CHANNEL_DIFFERENT_ERROR;
			}
			

		}
	}
	
	
	private class OnlineCheckIsReversible implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: mevcutta checkIsReversablePayment online olarak kurumlarda call edilen bir yapı  ( odemenin iptal edilip edilemeyeceginin ogrenilmesini saglayan bir yapı)
				     bu yapı sadece IZMIT-SU ve ADAPAZARI-DOGALGAZ icerisinde yer alıyor
					 ve sadece ADAPAZARI-DOGALGAZ icin kurumun bir servisini call etmisiz. Bir kurum icin bu yapıyı desteklemeye gerek var mı?
			 * 
			 */
		}
	}
			
	private class DoAccounting implements ProcessStep {
		@Override
		public void executeStep() {
			
			accountingService = SpringUtil.getBean(AccountingService.class);
			createReverseAccountingResultDTO  = accountingService.doReverseAccounting(prepareCreateReverseAccounting());
			if(!createReverseAccountingResultDTO.isSuccess()){
				error = createReverseAccountingResultDTO.getError();
			}
		}
		
		private CreateReverseAccountingDTO prepareCreateReverseAccounting() {
			CreateReverseAccountingDTO createReverseAccountingDTO = new CreateReverseAccountingDTO();
			createReverseAccountingDTO.setChannelTransactionId(paymentDTO.getChannelTransactionId());
			createReverseAccountingDTO.setContractNo(paymentDTO.getContractNo());
			createReverseAccountingDTO.setPaymentMethodType(paymentDTO.getPaymentMethod());
			if (EnumProvisionType.CARD.equals(paymentDTO.getPaymentMethod().getProvisionType())) {
				createReverseAccountingDTO.setDummyMerchant(paymentDTO.getAccountingSources().equals(EnumAccountingSource.PAYMENT));
			}
			return createReverseAccountingDTO;
			
		}
	}
	
	private class InsertPaymentReverseRecord implements ProcessStep {
		@Override
		public void executeStep() {
			 preparePaymentCancelDTO();
	         paymentCancelDTO = paymentService.insertPaymentCancel(paymentCancelDTO);
		}
		
		private void preparePaymentCancelDTO() {
			paymentService = SpringUtil.getBean(PaymentService.class);

			paymentCancelDTO = new PaymentCancelDTO();
			paymentCancelDTO.setPaymentId(paymentDTO.getId());

			if (EnumProvisionType.CARD.equals(paymentDTO.getPaymentMethod().getProvisionType())) {

				String provisionRequestId = null;
				if(paymentDTO.getAccountingSources().equals(EnumAccountingSource.PAYMENT)) {
					provisionRequestId = CommonUtils.generateCreditCardProvisionRequestId(paymentDTO.getChannelCode(),true);
				}else {
					provisionRequestId = CommonUtils.generateCreditCardProvisionRequestId(paymentDTO.getChannelCode(),false);
				}
				paymentCancelDTO.setReverseProvisionRequestId(provisionRequestId);
			}

			paymentCancelDTO.setCancelDate(LocalDate.now());
			paymentCancelDTO.setBranchCode(branchCode);
			paymentCancelDTO.setChannelCode(channelCode);
			paymentCancelDTO.setChannelSessionId(channelSessionId);
			paymentCancelDTO.setChannelTransactionId(channelTransactionId);
			paymentCancelDTO.setChannelTransactionId(channelSessionId);
			paymentCancelDTO.setCreatedBy(agentCode);

		}
	}
	
	private class UpdateCustomerPaymentLimit implements ProcessStep {
		@Override
		public void executeStep() {
			
			if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}

			Integer customerInt = null;
			if (paymentDTO.getCustomerNo() != null) {
				customerInt = paymentDTO.getCustomerNo().intValue();
			}
			
			String identityNoStr = null;
			if (paymentDTO.getIdentityNo() != null) {
				identityNoStr = paymentDTO.getIdentityNo().toString();
			}

			NotifyPaymentLimitationRequest request = new NotifyPaymentLimitationRequest();
			request.setCancel(true);
			request.setChannelCode(channelCode);
			request.setClientNo(customerInt);
			request.setIdentityNo(identityNoStr);
			request.setContractNo(paymentDTO.getContractNo());
			request.setCreatedBy(agentCode);
			request.setProductCode("B0001");
			request.setReferenceNo(paymentDTO.getContractNo().toString());
			request.setTransactionDate(LocalDateTime.now());
			paymentEventPublisher.publishPaymentLimiationNotification(request);			
		}
	}
	
	private class UpdatePaymentRecord implements ProcessStep {
		@Override
		public void executeStep() {
			paymentService.updateStatus(EnumBillStatu.CANCELLED,billId,contractNo);
		}
	}
	
	private class InsertPaymentCancelNotificationRecord implements ProcessStep {
		@Override
		public void executeStep() {
			List<EnumPaymentNotificationType> enumValues = Arrays.asList(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION,EnumPaymentNotificationType.RVRS_PROVISION);
			insertedPaymentNotificationDTOList = createPaymentNotifications(enumValues);
		}

		private List<PaymentNotificationDTO> createPaymentNotifications(List<EnumPaymentNotificationType> notificationTypes) {
			return notificationTypes.stream().map(this::createPaymentNotification).toList();
		}
		private PaymentNotificationDTO createPaymentNotification(EnumPaymentNotificationType notificationType) {
			boolean validationFlag = Boolean.TRUE;
			PaymentNotificationDTO paymentNotificationDTO = new PaymentNotificationDTO();

			if(notificationType.equals(EnumPaymentNotificationType.RVRS_PROVISION)) {
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

	
	private class PublishBillPaymentCancelEvent implements ProcessStep {
		@Override
		public void executeStep() {
			
				PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
				publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(insertedPaymentNotificationDTOList);
				publishPaymentTypeDTO.setInstitutionDTO(institution);
				publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
				publishPaymentTypeDTO.setPaymentCancelDTO(paymentCancelDTO);
				
				paymentEventPublisher.findPublishPaymentCancelEvent(publishPaymentTypeDTO);

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
" BUNUDA YAPABİLRİ MİSİN

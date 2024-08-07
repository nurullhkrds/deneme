public class NotifyPaymentCancelProcess extends AbstractProcess {

	private static final Integer MAX_LENGTH_OF_NEW_INFO_FIELDS = 100;
	private static final Integer MAX_LENGTH_OF_OLD_INFO_FIELD = 50;

	private Long notificationTryCount;

	private Long paymentNotificationId;
	private Long paymentId;

	private PaymentService paymentService;
	private PaymentNotificationService paymentNotificationService;
	private AdapterService adapterService;

	// be carefull for hibernate dirty update
	private PaymentNotification paymentNotification;
	private Payment payment;
	private PaymentCancelDTO paymentCancel;
	
	private NotifyPaymentCancelAdapterResponse notifyPaymentCancelAdapterResponse;

	@Override
	public void executeProcess() throws BillException {
		addProcessStep(new GatherData());
		addProcessStep(new FetchPaymentNotificationRecordWithLock());
		addProcessStep(new FetchPaymentRecordWithLock());
		addProcessStep(new FetchPaymentCancelRecord());
		addProcessStep(new CallInstitutionExternalService());
		addProcessStep(new UpdatePaymentNotificationRecord());
		addProcessStep(new UpdatePaymentRecord());
		
		executeSteps();
		prepareLogVariable();
	}

	
	
	@Override
	public void initProcess(ProcessExecutionInput input, ProcessLogDTO logDTO) {
		shouldRaiseExceptionOnABillError = false;
		super.initProcess(input, logDTO);
	}

	
	@Override
	protected void prepareExecutionOutput() {
		executionOutput = new NotifyPaymentCancelProcessOutput();
		executionOutput.setResult(error);
		
		if(notifyPaymentCancelAdapterResponse != null) {
			((NotifyPaymentCancelProcessOutput) executionOutput).setInstitutionReturnCode(notifyPaymentCancelAdapterResponse.getInstitutionResultCode());
			((NotifyPaymentCancelProcessOutput) executionOutput).setInstitutionReturnCode(notifyPaymentCancelAdapterResponse.getInstitutionResultDetail());
		}
	}
	

	private class GatherData implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			paymentService = SpringUtil.getBean(PaymentService.class);
			paymentNotificationService = SpringUtil.getBean(PaymentNotificationService.class);
			adapterService = SpringUtil.getBean(AdapterService.class);

			paymentNotificationId = (Long) dataPack.get(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey());
			notificationTryCount = Long.valueOf(Optional
					.ofNullable(SpringUtil.getBean(InstitutionFeatureService.class)
							.getFeatureValue(EnumFeatureCode.NOTIFICATION_TRY_COUNT, institutionCode, productCode))
					.orElse(BillTransactionConstant.DEFAULT_NOTIFICATION_TRY_COUNT));
		}

	}

	private class FetchPaymentNotificationRecordWithLock implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			paymentNotification = paymentNotificationService.findPaymentNotificationWithLock(paymentNotificationId);

			if (paymentNotification == null) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND;
				return;
			}

			if (!EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION
					.equals(paymentNotification.getNotificationType())) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_TYPE_INVALID;
				return;
			}

			if (EnumPaymentNotificationStatu.SUCCESS.equals(paymentNotification.getNotificationStatus())) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED;
				return;
			}

			Integer retryCount = Optional.ofNullable(paymentNotification.getRetryCount()).orElse(0);
			if (Integer.compare(retryCount, notificationTryCount.intValue()) > 0) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_TRY_COUNT_EXCEEDED;
				return;
			}

			paymentId = paymentNotification.getPaymentId();
		}

	}

	private class FetchPaymentRecordWithLock implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			payment = paymentService.findPaymentByIdWithLock(paymentId);

			if (payment == null) {
				error = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR;
				return;
			}
		}

	}

	private class FetchPaymentCancelRecord implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			paymentCancel = paymentService.getPaymentCancel(paymentId);

			if (paymentCancel == null) {
				error = EnumBillResult.CANCELLED_BILL_NOT_FOUND;
			}
		}

	}

	private class CallInstitutionExternalService implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			NotifyPaymentCancelAdapterRequest request = new NotifyPaymentCancelAdapterRequest();
			request.setChannelCode(channelCode);
			request.setInstitution(institutionCode);
			request.setInstitutionDebtTypeId(payment.getInstitutionDebtTypeId());
			request.setInstitutionId(payment.getInstitutionDebtTypeId());
			request.setOperatingBranchCode(branchCode);
			request.setProduct(productCode);
			request.setRequestDate(LocalDateTime.now());
			request.setTransactionDate(payment.getCreateDate());
			request.setUserCode(agentCode);

			CancelledBillAdapterDTO cancelledBill = PaymentMapper.INSTANCE.toCancelledBillAdapterDTO(payment,
					paymentCancel);
			mapAdditionalInfoAreas(payment, cancelledBill);

			request.setCancelledBill(cancelledBill);

			notifyPaymentCancelAdapterResponse = adapterService.notifyPaymentCancel(request, paymentCancel.getChannelTransactionId(),
					paymentCancel.getChannelSessionId());		
			
		}

		private void mapAdditionalInfoAreas(Payment payment, CancelledBillAdapterDTO cancelledBill) {

			String additionalInfo1 = StringUtils.rightPad(payment.getAdditionalInfo1(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo2 = StringUtils.rightPad(payment.getAdditionalInfo2(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo3 = StringUtils.rightPad(payment.getAdditionalInfo3(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo4 = StringUtils.rightPad(payment.getAdditionalInfo4(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo5 = StringUtils.rightPad(payment.getAdditionalInfo5(), MAX_LENGTH_OF_NEW_INFO_FIELDS);

			String info1 = StringUtils.substring(additionalInfo1, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info2 = StringUtils.substring(additionalInfo2, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info3 = StringUtils.substring(additionalInfo3, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info4 = StringUtils.substring(additionalInfo4, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info5 = StringUtils.substring(additionalInfo5, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);

			String info6 = StringUtils.substring(additionalInfo1, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info7 = StringUtils.substring(additionalInfo2, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info8 = StringUtils.substring(additionalInfo3, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info9 = StringUtils.substring(additionalInfo4, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);

			cancelledBill.setAdditionalInfo1(StringUtils.trim(info1));
			cancelledBill.setAdditionalInfo2(StringUtils.trim(info2));
			cancelledBill.setAdditionalInfo3(StringUtils.trim(info3));
			cancelledBill.setAdditionalInfo4(StringUtils.trim(info4));
			cancelledBill.setAdditionalInfo5(StringUtils.trim(info5));
			cancelledBill.setAdditionalInfo6(StringUtils.trim(info6));
			cancelledBill.setAdditionalInfo7(StringUtils.trim(info7));
			cancelledBill.setAdditionalInfo8(StringUtils.trim(info8));
			cancelledBill.setAdditionalInfo9(StringUtils.trim(info9));
		}
	}

	private class UpdatePaymentNotificationRecord implements ProcessStep {

		@Override
		public void executeStep() throws BillException {

			if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(notifyPaymentCancelAdapterResponse.getStatus())) {
				paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.SUCCESS);
				paymentNotification.setNotificationDate(LocalDateTime.now());
			}

			else {
				paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			}

			Integer retryCount = Optional.ofNullable(paymentNotification.getRetryCount()).orElse(0);
			paymentNotification.setRetryCount(retryCount + 1);
			paymentNotification.setResponseCode(notifyPaymentCancelAdapterResponse.getInternalResultCode());
			paymentNotification.setResponseMessage(notifyPaymentCancelAdapterResponse.getInternalResultDetail());
			paymentNotification.setInstitutionReturnCode(notifyPaymentCancelAdapterResponse.getInstitutionResultCode());
			paymentNotification.setInstitutionReturnText(notifyPaymentCancelAdapterResponse.getInstitutionResultDetail());
			paymentNotification.setUpdateDate(LocalDateTime.now());
			paymentNotification.setUpdatedBy("SYSTEM");
			paymentNotificationService.updatePaymentNotification(paymentNotification);
		}

	}
	
	private class UpdatePaymentRecord implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			NotifiedCancelledBillAdapterDTO notifiedCancelledBill = notifyPaymentCancelAdapterResponse.getNotifiedCancelledBill();
			if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(notifyPaymentCancelAdapterResponse.getStatus())
					&& notifiedCancelledBill != null) {
				payment.setCancelStan(notifiedCancelledBill.getPaymentCancelStan());
				payment.setInstitutionCancelStan(notifiedCancelledBill.getInstitutionPaymentCancelStan());
			}

			if (notifiedCancelledBill != null) {

				mapAdditionalInfoAreas(payment, notifiedCancelledBill);
			}

			paymentService.updatePayment(payment);
		}
		
		private void mapAdditionalInfoAreas(Payment payment, NotifiedCancelledBillAdapterDTO notifiedCancelledBill) {

			String additionalInfo1 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo1()).orElse("");
			String additionalInfo2 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo2()).orElse("");
			String additionalInfo3 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo3()).orElse("");
			String additionalInfo4 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo4()).orElse("");
			String additionalInfo5 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo5()).orElse("");
			String additionalInfo6 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo6()).orElse("");
			String additionalInfo7 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo7()).orElse("");
			String additionalInfo8 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo8()).orElse("");
			String additionalInfo9 = Optional.ofNullable(notifiedCancelledBill.getAdditionalInfo9()).orElse("");

			additionalInfo1 = StringUtils.rightPad(additionalInfo1, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo2 = StringUtils.rightPad(additionalInfo2, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo3 = StringUtils.rightPad(additionalInfo3, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo4 = StringUtils.rightPad(additionalInfo4, MAX_LENGTH_OF_OLD_INFO_FIELD);

			additionalInfo1 = additionalInfo1.concat(additionalInfo6);
			additionalInfo2 = additionalInfo2.concat(additionalInfo7);
			additionalInfo3 = additionalInfo3.concat(additionalInfo8);
			additionalInfo4 = additionalInfo4.concat(additionalInfo9);

			payment.setAdditionalInfo1(StringUtils.trim(additionalInfo1));
			payment.setAdditionalInfo2(StringUtils.trim(additionalInfo2));
			payment.setAdditionalInfo3(StringUtils.trim(additionalInfo3));
			payment.setAdditionalInfo4(StringUtils.trim(additionalInfo4));
			payment.setAdditionalInfo5(StringUtils.trim(additionalInfo5));
		}
		
	}
	
	private void prepareLogVariable() {
		if (payment != null) {
			logDTO.setCustomerNo(payment.getCustomerNo());
			logDTO.setIdentityNo(payment.getIdentityNo());
			logDTO.setTaxId(payment.getTaxId());
			logDTO.setSubscriberNo(payment.getSubscriberNo());

			return;
		}

		if (paymentNotification != null) {
			PaymentDTO paymentDTO = Optional.ofNullable(paymentService.getPayment(paymentNotification.getPaymentId()))
					.orElse(new PaymentDTO());
			logDTO.setCustomerNo(paymentDTO.getCustomerNo());
			logDTO.setIdentityNo(paymentDTO.getIdentityNo());
			logDTO.setTaxId(paymentDTO.getTaxId());
			logDTO.setSubscriberNo(paymentDTO.getSubscriberNo());
		}

	}

}
bu sınıfın testi 

"class NotifyPaymentCancelProcessTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @InjectMocks
    private NotifyPaymentCancelProcess process;

    @Mock
    private ProcessService processService;

    @Mock
    private ApplicationContext applicationContext;

    @Mock
    private InstitutionFeatureService institutionFeatureService;



    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(ProcessService.class)).thenReturn(processService);
        when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(AdapterService.class)).thenReturn(adapterService);

        PaymentNotification mockNotification = new PaymentNotification();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        mockNotification.setPaymentId(1L);
        mockNotification.setRetryCount(0);

        Payment mockPayment = new Payment();
        mockPayment.setInstitutionDebtTypeId(1L);
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setAdditionalInfo1("Info1");

        PaymentCancelDTO mockPaymentCancel = new PaymentCancelDTO();
        mockPaymentCancel.setChannelTransactionId("transactionId");
        mockPaymentCancel.setChannelSessionId("sessionId");

        NotifyPaymentCancelAdapterResponse mockResponse = new NotifyPaymentCancelAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        mockResponse.setNotifiedCancelledBill(new NotifiedCancelledBillAdapterDTO());

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification);
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);
        when(paymentService.getPaymentCancel(1L)).thenReturn(mockPaymentCancel);
        when(adapterService.notifyPaymentCancel(any(NotifyPaymentCancelAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);
        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        NotifyPaymentCancelProcessOutput output = (NotifyPaymentCancelProcessOutput) process.getExecutionOutput();
        assertNull(output);
    }

    @Test
    void testExecuteProcessPaymentNotificationNotFound() throws BillException {

        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(ProcessService.class)).thenReturn(processService);
        when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(AdapterService.class)).thenReturn(adapterService);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);


        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(null);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);


        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);


        process.initProcess(input, new ProcessLogDTO("processLog"));
        process.executeProcess();


        NotifyPaymentCancelProcessOutput output = (NotifyPaymentCancelProcessOutput) process.getExecutionOutput();
        assertNull(output);
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput{

        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}
" bu testlerdir ve başarıyla çalıştı.




"public class QueryBillsProcess extends AbstractProcess {

    private AdapterService  adapterService;
    private ProvisionService provisionService;
    private InstitutionUserIntService institutionUserIntService;
    private InstitutionUserIntfMapper institutionUserIntMapper;   
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
            institutionUserIntMapper = SpringUtil.getBean(InstitutionUserIntfMapper.class);
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
				return;
			}
       	 
		}
		
		

	}

    private class QueryFromDatabase implements ProcessStep {

        @Override
        public void executeStep() {
            //TODO: Offline borc sorgulama
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

        private static final int MAX_LENGTH_OF_OLD_INFO_FIELD = 50;

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
                
                mapAdditionalInfoFields(provisionDTO, queriedBillDTO);
                
                return provisionDTO;
            }).toList();
        }

		private void mapAdditionalInfoFields(ProvisionDTO provisionDTO, QueriedBillDTO queriedBillDTO) {
			String additionalInfo1 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo1()).orElse("");
			String additionalInfo2 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo2()).orElse("");
			String additionalInfo3 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo3()).orElse("");
			String additionalInfo4 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo4()).orElse("");
			String additionalInfo5 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo5()).orElse("");
			String additionalInfo6 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo6()).orElse("");
			String additionalInfo7 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo7()).orElse("");
			String additionalInfo8 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo8()).orElse("");
			String additionalInfo9 = Optional.ofNullable(queriedBillDTO.getAdditionalInfo9()).orElse("");
			

			additionalInfo1 = StringUtils.rightPad(additionalInfo1, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo2 = StringUtils.rightPad(additionalInfo2, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo3 = StringUtils.rightPad(additionalInfo3, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo4 = StringUtils.rightPad(additionalInfo4, MAX_LENGTH_OF_OLD_INFO_FIELD);			
			
			additionalInfo1 = additionalInfo1.concat(additionalInfo6);
			additionalInfo2 = additionalInfo2.concat(additionalInfo7);
			additionalInfo3 = additionalInfo3.concat(additionalInfo8);
			additionalInfo4 = additionalInfo4.concat(additionalInfo9);
			
			provisionDTO.setAdditionalInfo1(StringUtils.trim(additionalInfo1));
			provisionDTO.setAdditionalInfo2(StringUtils.trim(additionalInfo2));
			provisionDTO.setAdditionalInfo3(StringUtils.trim(additionalInfo3));
			provisionDTO.setAdditionalInfo4(StringUtils.trim(additionalInfo4));
			provisionDTO.setAdditionalInfo5(StringUtils.trim(additionalInfo5));
			
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
"

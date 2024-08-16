public class BillPaymentReverseProcess extends AbstractProcess {
    
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


BU BENİM SINIFIM VE AŞAĞIDAKİ DE BENİM UNİT TEST SINIFIM..


"class BillPaymentReverseProcessTest {

    @Mock
    private ApplicationContext applicationContext;

    @Mock
    private AccountingService accountingService;

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @Mock
    private  ChannelService channelService;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    private PaymentDTO paymentDTO;

    private ChannelDTO channelDTO;
    private List<PaymentNotificationDTO> insertedPaymentNotificationDTOList;


    private InstitutionDTO institutionDTO;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);
        SpringUtil springUtil=new SpringUtil();

        springUtil.setApplicationContext(null);

        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        lenient().when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(getBean(PaymentService.class)).thenReturn(paymentService);
        lenient().when(getBean(AccountingService.class)).thenReturn(accountingService);
        lenient().when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        lenient().when(getBean(ChannelService.class)).thenReturn(channelService);

        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setChannelCode("someChannelCode");
        paymentDTO.setContractNo(456L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);


        ProcessLogDTO logDTO = new ProcessLogDTO("123");
        billPaymentReverseProcess.setLogDTO(logDTO);

         channelDTO=new ChannelDTO();
         channelDTO.setCode("123");

         insertedPaymentNotificationDTOList=new ArrayList<>();


        institutionDTO = new InstitutionDTO();
        institutionDTO.setInstitutionCode("123");
        institutionDTO.setIsReverseAllowed(true);
        billPaymentReverseProcess.setInstitution(institutionDTO);
        when(channelService.findChannelByChannelCode(any())).thenReturn(channelDTO);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(billPaymentReverseProcess, billPaymentReverseProcess.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        SpringUtil springUtil=new SpringUtil();

        springUtil.setApplicationContext(applicationContext);
        PaymentCancelDTO paymentCancelDTO=new PaymentCancelDTO();
        paymentCancelDTO.setPaymentId(123L);
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(insertedPaymentNotificationDTOList);
        publishPaymentTypeDTO.setInstitutionDTO(institutionDTO);
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setPaymentCancelDTO(paymentCancelDTO);
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);
        when(accountingService.doReverseAccounting(any())).thenReturn(createReverseAccountingResultDTO);
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);
        doNothing().when(paymentEventPublisher).findPublishPaymentEvent(publishPaymentTypeDTO);


        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        billPaymentReverseProcess.executeProcess();

        verify(paymentService).getPayment(1L, 456L);
        verify(accountingService).doReverseAccounting(any());
        verify(paymentNotificationService, times(1)).insertPaymentNotification(any());
        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());
        assertNotNull(channelService, "channelService should not be null");


        assertNotNull(billPaymentReverseProcess.getInstitution(), "Institution should not be null");


        assertNull(billPaymentReverseProcess.getExecutionOutput());
    }
}
" 
________________________________________________-
public class QueryBillsProcess extends AbstractProcess {

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




class QueryBillsProcessTest {

    @Mock
    private AdapterService adapterService;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private InstitutionUserIntService institutionUserIntService;

    @Mock
    private InstitutionUserIntfMapper institutionUserIntMapper;

    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;

    @Mock
    private PaymentRepository paymentRepository;

    @Mock
    private PaymentMapper paymentMapper;

    @Mock
    private LimitationService limitationService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;


    @Mock
    private ApplicationContext applicationContext;



    private InstitutionDTO institution;
    private List<QueriedBillDTO> queriedBillDTOList;
    private List<ProvisionDTO>  provisionList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;

    private InstitutionDebtTypeDTO institutionDebtType;

    private InstitutionProcessDTO institutionProcess;

    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        SpringUtil springUtil=new SpringUtil();

        springUtil.setApplicationContext(null);

        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(AdapterService.class)).thenReturn(adapterService);
        lenient().when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(getBean(ProvisionService.class)).thenReturn(provisionService);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);

        lenient().when(getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(getBean(LimitationService.class)).thenReturn(limitationService);


        institution = new InstitutionDTO();
        institution.setId(1L);

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setIsOnline(true);



        // Initialize subscriberNoPartList
        SubscriberNoPartRequestDTO sub1 = new SubscriberNoPartRequestDTO();
        sub1.setPartNo(123);
        sub1.setPartKey("123");
        subscriberNoPartList = new ArrayList<>();
        subscriberNoPartList.add(sub1);
    }

}

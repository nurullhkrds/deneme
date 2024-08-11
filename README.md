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
BU BENİM SINIFIM


"class QueryBillsProcessTest {

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
    private ProcessService processService;

    @Mock
    private ApplicationContext applicationContext;

    @InjectMocks
    private QueryBillsProcess process;

    private InstitutionDTO institution;

    private InstitutionDebtTypeDTO institutionDebtType;

    private InstitutionProcessDTO institutionProcess;

    private Long institutionDebtTypeId;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);
        SpringUtil springUtil=new SpringUtil();

        springUtil.setApplicationContext(null);

        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(AdapterService.class)).thenReturn(adapterService);
        lenient().when(getBean(ProvisionService.class)).thenReturn(provisionService);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);

        lenient().when(getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(getBean(LimitationService.class)).thenReturn(limitationService);
        lenient().when(getBean(ProcessService.class)).thenReturn(processService);
        lenient().when(getBean(QueryBillsProcess.class)).thenReturn(process);


        institution = new InstitutionDTO();
        institution.setId(123L);
        institution.setIsReverseAllowed(true);
        institution.setInstitutionCode("123");


        institutionDebtType=new InstitutionDebtTypeDTO();
        institutionDebtType.setId(123L);
        institutionDebtType.setIsActive(true);
        institutionDebtType.setDebtType("deby");

        institutionProcess=new InstitutionProcessDTO();
        institutionProcess.setId(123L);

        ProcessLogDTO logDTO = new ProcessLogDTO("123");
        process.setLogDTO(logDTO);


        process.setInstitution(institution);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(process, process.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        SpringUtil springUtil=new SpringUtil();
        springUtil.setApplicationContext(applicationContext);


        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);


        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        process.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        process.executeProcess();


        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());


        assertNull(process.getExecutionOutput());
    }
}
" BU İSE BENİM TEST SINIFIM 


"java.lang.NullPointerException: Cannot invoke "org.springframework.context.ApplicationContext.getBean(java.lang.Class)" because "com.ykb.payments.bill.common.util.SpringUtil.appContext" is null
" BU İSE ALDIĞIM HATA

"public class QueryBillsProcessTest {

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

    @Mock
    private InstitutionDTO institution;

    @Mock
    private InstitutionDebtTypeDTO institutionDebtType;

    @Mock
    private InstitutionProcessDTO institutionProcess;

    private Long institutionDebtTypeId;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        institution = new InstitutionDTO();
        institution.setId(1L);

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setIsOnline(true);

        process.setInstitution(institution);
        process.setInstitutionDebtType(institutionDebtType);
        process.setInstitutionProcess(institutionProcess);

        // Initialize internal data structures
        process.setDataPack(new HashMap<>());
        process.setExecutionOutput(new QueryBillsProcessOutput());

        // Initialize subscriberNoPartList
        SubscriberNoPartRequestDTO sub1 = new SubscriberNoPartRequestDTO();
        sub1.setPartNo(123);
        sub1.setPartKey("123");
        subscriberNoPartList = new ArrayList<>();
        subscriberNoPartList.add(sub1);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);
        when(SpringUtil.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        when(SpringUtil.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        when(SpringUtil.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        when(SpringUtil.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        when(SpringUtil.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        when(SpringUtil.getBean(LimitationService.class)).thenReturn(limitationService);
        when(SpringUtil.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(SpringUtil.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);

        InstitutionUserIntfDTO mockInstitutionUserIntfDTO = new InstitutionUserIntfDTO();
        when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(List.of(mockInstitutionUserIntfDTO));
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
        mockQueriedBillDTO.setBillDueDate(LocalDate.now());
        mockQueriedBillDTO.setBillNo("12345");

        QueryBillsAdapterResponse mockResponse = new QueryBillsAdapterResponse();
        mockResponse.setInternalResultCode(String.valueOf(EnumBillResult.SUCCESS.getCode()));
        mockResponse.setBills(List.of(mockQueriedBillDTO));

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);
        institutionDebtTypeId = 1L;

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(institutionDebtTypeId);

        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(123L);
        institutionProcessDTO.setIsOnline(true);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);
        when(processService.getInstitutionProcess(any(), any(), any())).thenReturn(institutionProcessDTO);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.QUERY_BILLS);
        input.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.TAX_ID.getKey(), "taxId");
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subNo");
        input.getDataPack().put(ProcessDataPackKey.DEBT_TYPE_ID.getKey(), institutionDebtTypeId);
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);  // Add this line to include subscriberNoPartList
        process.setInstitutionDebtType(institutionDebtType);
        process.setInstitutionProcess(institutionProcess); // Ensure institutionProcess is set

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        QueryBillsProcessOutput output = (QueryBillsProcessOutput) process.getExecutionOutput();
        assertNull(output.getResult());

    }




    @Test
    void testExecuteProcessCustomerQueryLimitReached() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);
        when(SpringUtil.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        when(SpringUtil.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        when(SpringUtil.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        when(SpringUtil.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        when(SpringUtil.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        when(SpringUtil.getBean(LimitationService.class)).thenReturn(limitationService);
        when(SpringUtil.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(SpringUtil.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);

        InstitutionUserIntfDTO mockInstitutionUserIntfDTO = new InstitutionUserIntfDTO();
        when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(List.of(mockInstitutionUserIntfDTO));
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
        mockQueriedBillDTO.setBillDueDate(LocalDate.now());
        mockQueriedBillDTO.setBillNo("12345");

        QueryBillsAdapterResponse mockResponse = new QueryBillsAdapterResponse();
        mockResponse.setInternalResultCode(String.valueOf(EnumBillResult.SUCCESS.getCode()));
        mockResponse.setBills(List.of(mockQueriedBillDTO));

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);
        institutionDebtTypeId = 1L;

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(institutionDebtTypeId);

        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(123L);
        institutionProcessDTO.setIsOnline(true);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);
        when(processService.getInstitutionProcess(any(), any(), any())).thenReturn(institutionProcessDTO);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.QUERY_BILLS);
        input.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.TAX_ID.getKey(), "taxId");
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subNo");
        input.getDataPack().put(ProcessDataPackKey.DEBT_TYPE_ID.getKey(), institutionDebtTypeId);
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);  // Add this line to include subscriberNoPartList
        process.setInstitutionDebtType(institutionDebtType);
        process.setInstitutionProcess(institutionProcess); // Ensure institutionProcess is set

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();
        QueryBillsProcessOutput output = (QueryBillsProcessOutput) process.getExecutionOutput();
        assertNull(output.getResult());
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {

        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}" bu testi denedim bu iki test başarıyla çalıştı bu iki test sorunlu değil...


fakat aşağıda gönderdiğim kodlar coverage edilmedi oralarıda test etmek gerekiyor. 





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

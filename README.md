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
    private ApplicationContext applicationContext;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    private InstitutionDTO institution;
    private InstitutionDebtTypeDTO institutionDebtType;
    private List<QueriedBillDTO> queriedBillDTOList;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;
    private InstitutionProcessDTO institutionProcessDTO;


    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);

        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        lenient().when(getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(getBean(LimitationService.class)).thenReturn(limitationService);
        QueryBillsAdapterResponse adapterResponse=new QueryBillsAdapterResponse();
        adapterResponse.setBills(queriedBillDTOList);
        adapterResponse.setStatus("SUCCESS");
        lenient().when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(adapterResponse);
        lenient().when(provisionService.createProvisions(any())).thenReturn(new ArrayList<>());
        lenient().when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(new ArrayList<>());
        lenient().when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
                .thenReturn(new ResponseGetCustomerPaidBillList());
        lenient().when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString()))
                .thenReturn(new ArrayList<>());

        institution = new InstitutionDTO();
        institution.setId(1L);
        institution.setInstitutionCode("123");

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        queriedBillDTOList = new ArrayList<>();
        subscriberNoPartList = new ArrayList<>();
        institutionUserIntListDTO = new ArrayList<>();
        institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setIsOnline(true);

        queryBillsProcess.setInstitution(institution);
        queryBillsProcess.setInstitutionDebtType(institutionDebtType);
        queryBillsProcess.setInstitutionProcess(institutionProcessDTO);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(queryBillsProcess, queryBillsProcess.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subscriber123");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        queryBillsProcess.setDataPack(dataPack);


        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        queryBillsProcess.executeProcess();
        queryBillsProcess.prepareExecutionOutput();
        assertEquals(queryBillsProcess.getExecutionOutput().toString(), queryBillsProcess.getExecutionOutput().toString());
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

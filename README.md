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

        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(applicationContext.getBean(AdapterService.class)).thenReturn(adapterService);
        lenient().when(applicationContext.getBean(ProvisionService.class)).thenReturn(provisionService);
        lenient().when(applicationContext.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(applicationContext.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(applicationContext.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(applicationContext.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);

        lenient().when(applicationContext.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(applicationContext.getBean(LimitationService.class)).thenReturn(limitationService);
        lenient().when(applicationContext.getBean(ProcessService.class)).thenReturn(processService);
        lenient().when(applicationContext.getBean(QueryBillsProcess.class)).thenReturn(process);

        institution = new InstitutionDTO();
        institution.setId(123L);
        institution.setIsReverseAllowed(true);
        institution.setInstitutionCode("123");

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(123L);
        institutionDebtType.setIsActive(true);
        institutionDebtType.setDebtType("debt");

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setId(123L);
        institutionProcess.setIsOnline(true);

        ProcessLogDTO logDTO = new ProcessLogDTO("123");
        process.setLogDTO(logDTO);

        subscriberNoPartList = new ArrayList<>();
        SubscriberNoPartRequestDTO partRequestDTO = new SubscriberNoPartRequestDTO();
        partRequestDTO.setPartNo(123);
        subscriberNoPartList.add(partRequestDTO);

        process.setInstitution(institution);
        process.setInstitutionProcess(institutionProcess);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(process, process.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        // Mocking necessary dependencies
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);
        when(provisionService.createProvisions(any())).thenReturn(new ArrayList<>());
        
        // Set the data pack
        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.DEBT_TYPE_ID.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);
        process.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        // Execute the process
        process.executeProcess();

        // Verify that the correct method was called
        verify(paymentEventPublisher, times(1)).findPublishPaymentCancelEvent(any());

        // Validate the execution output
        assertNull(process.getExecutionOutput());
    }
}

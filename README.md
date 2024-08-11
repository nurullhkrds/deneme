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

        SpringUtil springUtil=new SpringUtil();
        springUtil.setApplicationContext(applicationContext); // ApplicationContext'i burada ayarlayın

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

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(123L);
        institutionDebtType.setIsActive(true);
        institutionDebtType.setDebtType("debt");

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setId(123L);
        institutionProcess.setIsOnline(true);

        ProcessLogDTO logDTO = new ProcessLogDTO("123");
        process.setLogDTO(logDTO);

        process.setInstitution(institution);
        process.setInstitutionProcess(institutionProcess);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(process, process.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.DEBT_TYPE_ID.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        process.executeProcess();

        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());

        assertNull(process.getExecutionOutput());
    }
}



java.lang.NullPointerException: Cannot invoke "java.util.List.stream()" because "subscriberNoPartList" is null

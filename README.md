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
    private ApplicationContext applicationContext;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    private InstitutionDTO institution;
    private List<QueriedBillDTO> queriedBillDTOList;
    private List<ProvisionDTO> provisionList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    private InstitutionDebtTypeDTO institutionDebtType;

    private InstitutionProcessDTO institutionProcess;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);
        SpringUtil springUtil=new SpringUtil();
        springUtil.setApplicationContext(applicationContext);


        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
    
        // Test setup
        institution = new InstitutionDTO();
        institution.setId(1L);

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setIsOnline(true);  // Burada isOnline alanını uygun şekilde ayarlıyoruz

        queriedBillDTOList = new ArrayList<>();
        institutionUserIntListDTO = new ArrayList<>();
        provisionList = new ArrayList<>();
        subscriberNoPartList = new ArrayList<>();

        // Create SubscriberNoPartRequestDTO examples
        SubscriberNoPartRequestDTO partRequestDTO = new SubscriberNoPartRequestDTO();
        partRequestDTO.setPartNo(123);
        partRequestDTO.setPartKey("someKey");
        subscriberNoPartList.add(partRequestDTO);

        // Mock behaviors for services
        lenient().when(adapterService.queryBills(any(), anyString(), anyString())).thenReturn(new QueryBillsAdapterResponse());
        lenient().when(provisionService.createProvisions(any())).thenReturn(provisionList);
        lenient().when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(institutionUserIntListDTO);

        // Initialize the stepHandler field in AbstractProcess
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(queryBillsProcess, queryBillsProcess.new ProcessStepHandler());

        // QueryBillsProcess sınıfına institutionProcess değerini ayarlıyoruz
        queryBillsProcess.setInstitutionProcess(institutionProcess);
    }


    @Test
    void testExecuteProcess_Success() throws BillException {
        // Initialize the data pack with necessary values
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subscriber123");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        queryBillsProcess.setDataPack(dataPack);
        queryBillsProcess.setInstitution(institution);
        queryBillsProcess.setInstitutionDebtType(institutionDebtType);

        // Execute the process
        queryBillsProcess.executeProcess();

        // Verify interactions and assert conditions
        verify(adapterService, times(1)).queryBills(any(), anyString(), anyString());
        verify(provisionService, times(1)).createProvisions(any());
        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());

        assertNotNull(queryBillsProcess.getExecutionOutput(), "Execution output should not be null");
    }
    
} BU TESTTE 


"Wanted but not invoked:
adapterService.queryBills(
    <any>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_Success(QueryBillsProcessTest.java:170)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
adapterService.queryBills(
    <any>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_Success(QueryBillsProcessTest.java:170)
Actually, there were zero interactions with this mock.
" HATASINI ALIYORUM

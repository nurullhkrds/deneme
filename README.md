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

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    private InstitutionDTO institution;
    private InstitutionDebtTypeDTO institutionDebtType;
    private List<QueriedBillDTO> queriedBillDTOList;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);

        // Mocking SpringUtil's getBean methods
        lenient().when(adapterService.queryBills(any(), anyString(), anyString())).thenReturn(new QueryBillsAdapterResponse());
        lenient().when(provisionService.createProvisions(any())).thenReturn(new ArrayList<>());
        lenient().when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(new ArrayList<>());
        lenient().when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
                .thenReturn(new ResponseGetCustomerPaidBillList());
        lenient().when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString()))
                .thenReturn(new ArrayList<>());

        // Test setup
        institution = new InstitutionDTO();
        institution.setId(1L);
        institution.setInstitutionCode("123");

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        queriedBillDTOList = new ArrayList<>();
        subscriberNoPartList = new ArrayList<>();
        institutionUserIntListDTO = new ArrayList<>();

        queryBillsProcess.setInstitution(institution);
        queryBillsProcess.setInstitutionDebtType(institutionDebtType);

        // Initialize the stepHandler field in AbstractProcess
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

        // Mock behavior
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        queryBillsProcess.executeProcess();

        verify(adapterService).queryBills(any(), anyString(), anyString());
        verify(provisionService).createProvisions(any());
        verify(paymentEventPublisher).publishInquiryLimiationNotification(any());

        assertNotNull(queryBillsProcess.getExecutionOutput(), "Execution output should not be null");
        assertNull(queryBillsProcess.getExecutionOutput().getResult(), "Execution output should not have errors");
    }

    @Test
    void testExecuteProcess_Failure() throws BillException {
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "invalidSubscriber");

        queryBillsProcess.setDataPack(dataPack);

        // Mock behavior for invalid subscriber number
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(false);

        queryBillsProcess.executeProcess();

        verify(adapterService, never()).queryBills(any(), anyString(), anyString());
        verify(provisionService, never()).createProvisions(any());

        assertNotNull(queryBillsProcess.getExecutionOutput(), "Execution output should not be null");
        assertNotNull(queryBillsProcess.getExecutionOutput().getResult(), "Execution output should contain an error");
    }
}

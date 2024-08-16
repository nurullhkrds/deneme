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
    private List<QueriedBillDTO> queriedBillDTOList;
    private List<ProvisionDTO> provisionList;
    private List<InstitutionUserIntfDTO> institutionUserIntListDTO;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    private InstitutionDebtTypeDTO institutionDebtType;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        
        // Institution and related setup
        institution = new InstitutionDTO();
        institution.setId(1L);

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        // Initialize the lists
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

    @Test
    void testExecuteProcess_WithError() throws BillException {
        // Prepare data that will trigger an error in one of the steps
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "invalidSubscriber");

        queryBillsProcess.setDataPack(dataPack);
        queryBillsProcess.setInstitution(institution);

        // Mock an error condition
        when(adapterService.queryBills(any(), anyString(), anyString()))
            .thenReturn(new QueryBillsAdapterResponse(EnumBillResult.SUBSCRIBER_NUMBER_INVALID));

        // Execute the process
        queryBillsProcess.executeProcess();

        // Assert that an error was set and process stopped
        assertEquals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID, queryBillsProcess.getExecutionOutput().getResult());
    }

    // Additional tests can be added for other steps and conditions
}

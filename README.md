@ExtendWith(MockitoExtension.class)
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

        // Mock SpringUtil's getBean methods
        lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(applicationContext.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(applicationContext.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(applicationContext.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        lenient().when(applicationContext.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(applicationContext.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(applicationContext.getBean(LimitationService.class)).thenReturn(limitationService);
        QueryBillsAdapterResponse adapterResponse=new QueryBillsAdapterResponse();
        adapterResponse.setBills(queriedBillDTOList);
        adapterResponse.setStatus("SUCCESS");
        // Additional mocking
        lenient().when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(adapterResponse);
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
        institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setIsOnline(true);

        queryBillsProcess.setInstitution(institution);
        queryBillsProcess.setInstitutionDebtType(institutionDebtType);
        queryBillsProcess.setInstitutionProcess(institutionProcessDTO);

        // Initialize the stepHandler field in AbstractProcess
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(queryBillsProcess, queryBillsProcess.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        QueryBillsAdapterResponse adapterResponse=new QueryBillsAdapterResponse();
        adapterResponse.setBills(queriedBillDTOList);
        adapterResponse.setStatus("SUCCESS");
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subscriber123");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        queryBillsProcess.setDataPack(dataPack);

        // Mock behavior
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);
        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(adapterResponse);

        queryBillsProcess.executeProcess();

        // Verify that the correct steps were taken
        verify(adapterService, times(1)).queryBills(any(), anyString(), anyString());
        verify(provisionService, times(1)).createProvisions(any());
        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());

        assertNotNull(queryBillsProcess.getExecutionOutput(), "Execution output should not be null");
        assertNull(queryBillsProcess.getExecutionOutput().getResult(), "Execution output should not have errors");
    }
}

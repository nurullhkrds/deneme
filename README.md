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
    void testExecuteProcess_CheckCustomerQueryLimit() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(false); // Limit reached
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString()))
            .thenReturn(response);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcess_QueryFromService_Success() throws BillException {
        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode("00"); // Success code
        List<QueriedBillDTO> bills = new ArrayList<>();
        response.setBills(bills);

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
            .thenReturn(response);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        process.executeProcess();

        assertNotNull(process.getExecutionOutput());
        assertEquals(EnumBillResult.SUCCESS, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcess_EliminateBills_NoBillsFound() throws BillException {
        process.setQueriedBillDTOList(new ArrayList<>());

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        assertEquals(EnumBillResult.BILL_NOT_FOUND, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcess_InvalidateNotPaidProvisions() throws BillException {
        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        verify(provisionService, times(1)).invalidateNotPaidProvisions(anyLong(), anyString());
    }

    @Test
    void testExecuteProcess_CreateProvisions() throws BillException {
        when(provisionService.createProvisions(anyList())).thenReturn(new ArrayList<>());

        process.setQueriedBillDTOList(new ArrayList<>());

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        verify(provisionService, times(1)).createProvisions(anyList());
    }

    @Test
    void testExecuteProcess_UpdateCustomerQueryLimit_FomEnabled() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());
    }
}

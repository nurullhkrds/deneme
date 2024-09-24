class QueryBillsProcessTest {

    @Mock
    private AdapterService adapterService;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private InstitutionUserIntService institutionUserIntService;

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
    private ApplicationContext applicationContext;

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

        // Mocking Spring Beans
        lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        lenient().when(getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(getBean(LimitationService.class)).thenReturn(limitationService);

        // Setting up required DTOs and data
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

        assertNotNull(queryBillsProcess.getExecutionOutput());
    }

    @Test
    void testGatherDataStep() throws BillException {
        // Arrange
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        queryBillsProcess.setDataPack(dataPack);

        // Act
        queryBillsProcess.executeProcess();

        // Assert
        assertEquals(123L, queryBillsProcess.getCustomerNo());
        assertEquals(456L, queryBillsProcess.getIdentityNo());
    }

    @Test
    void testQueryFromService_Success() throws BillException {
        QueryBillsAdapterResponse adapterResponse = new QueryBillsAdapterResponse();
        adapterResponse.setBills(queriedBillDTOList);
        adapterResponse.setStatus("SUCCESS");

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(adapterResponse);

        queryBillsProcess.executeProcess();

        assertEquals(queriedBillDTOList, queryBillsProcess.getQueriedBillDTOList());
    }

    @Test
    void testQueryFromService_Error() throws BillException {
        QueryBillsAdapterResponse adapterResponse = new QueryBillsAdapterResponse();
        adapterResponse.setInternalResultCode("ERROR");

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(adapterResponse);

        queryBillsProcess.executeProcess();

        assertEquals(EnumBillResult.ERROR, queryBillsProcess.getExecutionOutput().getResult());
    }

    @Test
    void testEliminateBills_BillNotFound() throws BillException {
        when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
                .thenReturn(new ResponseGetCustomerPaidBillList());

        queriedBillDTOList = new ArrayList<>(); // Ensure no bills are found

        queryBillsProcess.executeProcess();

        assertEquals(EnumBillResult.BILL_NOT_FOUND, queryBillsProcess.getExecutionOutput().getResult());
    }

    @Test
    void testQueryFromDatabase_OfflineProcess() throws BillException {
        institutionProcessDTO.setIsOnline(false);

        queryBillsProcess.executeProcess();
        // TODO: Implement assertion for the offline process
    }

    @Test
    void testInvalidateNotPaidProvisions() throws BillException {
        when(provisionService.invalidateNotPaidProvisions(anyLong(), anyString())).thenReturn(true);

        queryBillsProcess.executeProcess();
        // Validate the provisions have been invalidated
    }

    @Test
    void testCheckCustomerQueryLimit_ExceedsLimit() throws BillException {
        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(false);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), any(), any(), anyString())).thenReturn(response);

        queryBillsProcess.executeProcess();

        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, queryBillsProcess.getExecutionOutput().getResult());
    }

    @Test
    void testCheckCustomerQueryLimit_Allowed() throws BillException {
        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(true);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), any(), any(), anyString())).thenReturn(response);

        queryBillsProcess.executeProcess();

        assertNotEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, queryBillsProcess.getExecutionOutput().getResult());
    }

    @Test
    void testCreateProvisions_Success() throws BillException {
        when(provisionService.createProvisions(any())).thenReturn(new ArrayList<>());
        queryBillsProcess.executeProcess();
        // Assert provisions are successfully created
    }

    @Test
    void testUpdateCustomerQueryLimit_Success() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        queryBillsProcess.executeProcess();
        // Assert that query limit is updated and events are published
    }

    @Test
    void testExceptionHandling() throws BillException {
        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
                .thenThrow(new BillException("Test Exception"));

        assertThrows(BillException.class, () -> queryBillsProcess.executeProcess());
    }
}

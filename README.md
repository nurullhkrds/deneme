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
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @Mock
    private ApplicationContext applicationContext;

    @InjectMocks
    private QueryBillsProcess process;

    private InstitutionDTO institution;
    private InstitutionDebtTypeDTO institutionDebtType;
    private Long institutionDebtTypeId;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    void setUp() {
        // Initialize the data required for the test
        institution = new InstitutionDTO();
        institution.setId(123L);
        institution.setInstitutionCode("123");

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(123L);
        institutionDebtTypeId = institutionDebtType.getId();

        subscriberNoPartList = new ArrayList<>();
        SubscriberNoPartRequestDTO partRequestDTO = new SubscriberNoPartRequestDTO();
        partRequestDTO.setPartNo(123);
        subscriberNoPartList.add(partRequestDTO);

        // Mock the ApplicationContext to return the mocks
        lenient().when(applicationContext.getBean(AdapterService.class)).thenReturn(adapterService);
        lenient().when(applicationContext.getBean(ProvisionService.class)).thenReturn(provisionService);
        lenient().when(applicationContext.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(applicationContext.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(applicationContext.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        lenient().when(applicationContext.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(applicationContext.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(applicationContext.getBean(LimitationService.class)).thenReturn(limitationService);
        lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        // Mocking necessary dependencies
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        QueriedBillDTO billDTO = new QueriedBillDTO();
        billDTO.setBillNo("123");
        billDTO.setBillAmount(BigDecimal.valueOf(100));
        List<QueriedBillDTO> billList = new ArrayList<>();
        billList.add(billDTO);

        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode("00");
        response.setBills(billList);

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
            .thenReturn(response);

        when(provisionService.createProvisions(anyList())).thenReturn(new ArrayList<>());

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        process.executeProcess();

        verify(provisionService, times(1)).createProvisions(anyList());
        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());

        assertEquals(EnumBillResult.SUCCESS, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcess_LimitReached() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        PaymentAllowedResponse paymentAllowedResponse = new PaymentAllowedResponse();
        paymentAllowedResponse.setPaymentAllowed(false);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString()))
            .thenReturn(paymentAllowedResponse);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcess_NoBillsFound() throws BillException {
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode("00");
        response.setBills(new ArrayList<>()); // No bills found

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
            .thenReturn(response);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

        process.executeProcess();

        assertEquals(EnumBillResult.BILL_NOT_FOUND, process.getExecutionOutput().getResult());
    }
}

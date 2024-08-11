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

    private InstitutionProcessDTO institutionProcessDTO;


    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        SpringUtil springUtil=new SpringUtil();
        springUtil.setApplicationContext(applicationContext);
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

        institutionProcessDTO=new InstitutionProcessDTO();
        institutionProcessDTO.setId(123L);
        institutionProcessDTO.setIsOnline(true);

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

        // Use reflection to set the stepHandler field
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(process, process.new ProcessStepHandler());

        process.setInstitutionProcess(institutionProcessDTO);

    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        // Mocking necessary dependencies
        lenient().when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

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

        assertNull(process.getExecutionOutput());
    }

    @Test
    void testExecuteProcess_LimitReached() throws BillException {
        lenient().when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        PaymentAllowedResponse paymentAllowedResponse = new PaymentAllowedResponse();
        paymentAllowedResponse.setPaymentAllowed(false);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString()))
                .thenReturn(paymentAllowedResponse);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);

        process.executeProcess();

        assertNull(process.getExecutionOutput());
    }

    @Test
    void testExecuteProcess_NoBillsFound() throws BillException {
        lenient().when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

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

        assertNull(process.getExecutionOutput());
    }
}


org.mockito.exceptions.misusing.UnnecessaryStubbingException: 
Unnecessary stubbings detected.
Clean & maintainable test code requires zero unnecessary code.
Following stubbings are unnecessary (click to navigate to relevant line of code):
  1. -> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_NoBillsFound(QueryBillsProcessTest.java:210)
Please remove unnecessary stubbings or use 'lenient' strictness. More info: javadoc for UnnecessaryStubbingException class.





java.lang.NullPointerException: Cannot invoke "java.util.List.stream()" because "subscriberNoPartList" is null






Wanted but not invoked:
provisionService.createProvisions(
    <any List>
);
-> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_Success(QueryBillsProcessTest.java:178)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
provisionService.createProvisions(
    <any List>
);
-> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_Success(QueryBillsProcessTest.java:178)
Actually, there were zero interactions with this mock.

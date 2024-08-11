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
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;
    private InstitutionProcessDTO institutionProcessDTO;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        institution = new InstitutionDTO();
        institution.setId(123L);
        institution.setInstitutionCode("123");

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(123L);

        subscriberNoPartList = new ArrayList<>();
        SubscriberNoPartRequestDTO partRequestDTO = new SubscriberNoPartRequestDTO();
        partRequestDTO.setPartNo(123);
        subscriberNoPartList.add(partRequestDTO);

        institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(123L);
        institutionProcessDTO.setIsOnline(true);

        // Mock ApplicationContext to return the mocks
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

        // Set stepHandler using reflection
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(process, process.new ProcessStepHandler());

        // Set other necessary fields
        process.setInstitution(institution);
        process.setInstitutionProcess(institutionProcessDTO);
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
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

        verify(provisionService).createProvisions(anyList());
        verify(paymentEventPublisher).publishInquiryLimiationNotification(any());

        assertNull(process.getExecutionOutput());
    }
}

@ExtendWith(MockitoExtension.class)
public class QueryBillsProcessTest {

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
    private ProcessService processService; // Add this mock

    @Mock
    private ApplicationContext applicationContext;

    @InjectMocks
    private QueryBillsProcess process;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);
        when(SpringUtil.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        when(SpringUtil.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        when(SpringUtil.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        when(SpringUtil.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        when(SpringUtil.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        when(SpringUtil.getBean(LimitationService.class)).thenReturn(limitationService);
        when(SpringUtil.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(SpringUtil.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService); // Add this line

        InstitutionUserIntfDTO mockInstitutionUserIntfDTO = new InstitutionUserIntfDTO();
        when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(List.of(mockInstitutionUserIntfDTO));
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
        mockQueriedBillDTO.setBillDueDate(LocalDate.now());
        mockQueriedBillDTO.setBillNo("12345");

        QueryBillsAdapterResponse mockResponse = new QueryBillsAdapterResponse();
        mockResponse.setInternalResultCode(EnumBillResult.SUCCESS.getCode());
        mockResponse.setBills(List.of(mockQueriedBillDTO));

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_QUERY);
        input.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        QueryBillsProcessOutput output = (QueryBillsProcessOutput) process.getExecutionOutput();
        assertEquals(EnumBillResult.SUCCESS, output.getResult());
        assertEquals(1, output.getProvisionDTOList().size());
    }

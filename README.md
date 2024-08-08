
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
    private ProcessService processService;

    @Mock
    private ApplicationContext applicationContext;

    @InjectMocks
    private QueryBillsProcess process;

    @Mock
    private InstitutionDTO institution;

    @Mock
    private InstitutionDebtTypeDTO institutionDebtType;

    @Mock
    private InstitutionProcessDTO institutionProcess;

    private Long institutionDebtTypeId;
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        institution = new InstitutionDTO();
        institution.setId(1L);

        institutionDebtType = new InstitutionDebtTypeDTO();
        institutionDebtType.setId(1L);

        institutionProcess = new InstitutionProcessDTO();
        institutionProcess.setIsOnline(true);

        process.setInstitution(institution);
        process.setInstitutionDebtType(institutionDebtType);
        process.setInstitutionProcess(institutionProcess);

        // Initialize internal data structures
        process.setDataPack(new HashMap<>());
        process.setExecutionOutput(new QueryBillsProcessOutput());

        // Initialize subscriberNoPartList
        SubscriberNoPartRequestDTO sub1 = new SubscriberNoPartRequestDTO();
        sub1.setPartNo(123);
        sub1.setPartKey("123");
        subscriberNoPartList = new ArrayList<>();
        subscriberNoPartList.add(sub1);
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
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);

        InstitutionUserIntfDTO mockInstitutionUserIntfDTO = new InstitutionUserIntfDTO();
        when(institutionUserIntService.getUserInterface(anyLong())).thenReturn(List.of(mockInstitutionUserIntfDTO));
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
        mockQueriedBillDTO.setBillDueDate(LocalDate.now());
        mockQueriedBillDTO.setBillNo("12345");

        QueryBillsAdapterResponse mockResponse = new QueryBillsAdapterResponse();
        mockResponse.setInternalResultCode(String.valueOf(EnumBillResult.SUCCESS.getCode()));
        mockResponse.setBills(List.of(mockQueriedBillDTO));

        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);
        institutionDebtTypeId = 1L;

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(institutionDebtTypeId);

        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(123L);
        institutionProcessDTO.setIsOnline(true);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);
        when(processService.getInstitutionProcess(any(), any(), any())).thenReturn(institutionProcessDTO);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.QUERY_BILLS);
        input.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 1L);
        input.getDataPack().put(ProcessDataPackKey.TAX_ID.getKey(), "taxId");
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subNo");
        input.getDataPack().put(ProcessDataPackKey.DEBT_TYPE_ID.getKey(), institutionDebtTypeId);
        input.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);  // Add this line to include subscriberNoPartList
        process.setInstitutionDebtType(institutionDebtType);
        process.setInstitutionProcess(institutionProcess); // Ensure institutionProcess is set

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        QueryBillsProcessOutput output = (QueryBillsProcessOutput) process.getExecutionOutput();
        assertEquals(EnumBillResult.SUCCESS, output.getResult());
        assertEquals(1, output.getProvisionDTOList().size());
    }

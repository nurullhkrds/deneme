@BeforeEach
void setUp() throws NoSuchFieldException, IllegalAccessException {
    MockitoAnnotations.openMocks(this);

    // Test setup
    institution = new InstitutionDTO();
    institution.setId(1L);

    institutionDebtType = new InstitutionDebtTypeDTO();
    institutionDebtType.setId(1L);

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

    // Initialize the stepHandler field in AbstractProcess
    Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
    stepHandlerField.setAccessible(true);
    stepHandlerField.set(queryBillsProcess, queryBillsProcess.new ProcessStepHandler());
}

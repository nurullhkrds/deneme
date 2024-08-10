@BeforeEach
public void setUp() {
    MockitoAnnotations.openMocks(this);

    // Mock nesneleri tanımla
    process.processService = processService;
    process.processChannel = processChannelDTO;
    process.institution = institutionDTO;
    process.institutionDebtType = institutionDebtTypeDTO;
    process.institutionChannel = institutionChannelDTO;
    process.institutionProcess = institutionProcessDTO;
    process.institutionChannelProcess = institutionChannelProcessDTO;
    process.logDTO = processLogDTO;

    // Varsayılan mock davranışlarını ayarla
    lenient().when(process.processChannel.getIsActive()).thenReturn(true);
    lenient().when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
    lenient().when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
    lenient().when(process.institution.getIsActive()).thenReturn(true);
    lenient().when(process.institutionProcess.getIsActive()).thenReturn(true);
    lenient().when(process.institutionChannel.getIsActive()).thenReturn(true);
    lenient().when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0)); // Burada ayarlanıyor
    lenient().when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0)); // Burada ayarlanıyor
    lenient().when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
    lenient().when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
    lenient().when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
    lenient().when(process.institutionDebtType.getIsActive()).thenReturn(true);
    lenient().when(process.logDTO.getResponseData1()).thenReturn(""); // responseData1'in null olmadığından emin olun
}

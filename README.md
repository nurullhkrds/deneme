@BeforeEach
public void setUp() {
    // Varsayılan mock davranışlarını ayarla
    lenient().when(processChannel.getIsActive()).thenReturn(true);
    lenient().when(processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0)); // 09:00
    lenient().when(processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0)); // 17:00
    lenient().when(institution.getIsActive()).thenReturn(true);
    lenient().when(institutionProcess.getIsActive()).thenReturn(true);
    lenient().when(institutionChannel.getIsActive()).thenReturn(true);
    lenient().when(institutionChannelProcess.getIsActive()).thenReturn(true);
    lenient().when(institutionDebtType.getIsActive()).thenReturn(true);

    // Mock nesnelerini ata
    process.processChannel = processChannel;
    process.institution = institution;
    process.institutionDebtType = institutionDebtType;
    process.institutionChannel = institutionChannel;
    process.institutionProcess = institutionProcess;
    process.institutionChannelProcess = institutionChannelProcess;
    process.channelCode = "channelCode";
    process.institutionDebtTypeId = 1L;

    // Test esnasında saat 10:00 olacak şekilde ayarla
    lenient().when(LocalTime.now()).thenReturn(LocalTime.of(10, 0)); // 10:00
}

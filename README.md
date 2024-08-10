import static org.mockito.Mockito.lenient;

@BeforeEach
public void setUp() {
    // Lenient stub'lar
    lenient().when(processChannel.getIsActive()).thenReturn(true);
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
}

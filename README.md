public class AbstractProcessTest {

    @InjectMocks
    private AbstractProcess process;

    @Mock
    private ProcessService processService;
    @Mock
    ProcessChannelDTO processChannelDTO;
    @Mock
    InstitutionDTO institutionDTO;
    @Mock
    InstitutionDebtTypeDTO institutionDebtTypeDTO;
    @Mock
    InstitutionChannelDTO institutionChannelDTO;
    @Mock
    InstitutionProcessDTO institutionProcessDTO;
    @Mock
    InstitutionChannelProcessDTO institutionChannelProcessDTO;
    @Mock
    ProcessLogDTO processLogDTO;
    @BeforeEach
    public void setUp() {
        // Mock nesneleri tanımla
        process.processService = processService;
        process.processChannel = processChannelDTO;
        process.institution = institutionDTO;
        process.institutionDebtType = institutionDebtTypeDTO;
        process.institutionChannel = institutionChannelDTO;
        process.institutionProcess = institutionProcessDTO;
        process.institutionChannelProcess =institutionChannelProcessDTO;
        process.logDTO = processLogDTO;

        // Varsayılan mock davranışlarını ayarla
        lenient().when(process.processChannel.getIsActive()).thenReturn(true);
        lenient().when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
        lenient().when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
        lenient().when(process.institution.getIsActive()).thenReturn(true);
        lenient().when(process.institutionProcess.getIsActive()).thenReturn(true);
        lenient().when(process.institutionChannel.getIsActive()).thenReturn(true);
        lenient().when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
        lenient().when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
        lenient().when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
        lenient().when(process.institutionDebtType.getIsActive()).thenReturn(true);
        lenient().when(process.logDTO.getResponseData1()).thenReturn(""); // responseData1'in null olmadığından emin olun
    }

    @Test
    public void testBeforeExecuteProcess_ProcessChannelNotFound() {
        process.processChannel = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_ProcessChannelNotActive() {
        when(process.processChannel.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_ProcessChannelNotWorkingHours() {
        when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
        when(LocalTime.now()).thenReturn(LocalTime.of(17, 30)); // Çalışma saatleri dışında bir zaman

        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionNotFound() {
        process.institution = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionNotActive() {
        when(process.institution.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionProcessNotFound() {
        process.institutionProcess = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionProcessNotActive() {
        when(process.institutionProcess.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotFound() {
        process.institutionChannelProcess = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotActive() {
        when(process.institutionChannelProcess.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotWorkingHours() {
        when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
        when(LocalTime.now()).thenReturn(LocalTime.of(17, 30)); // Çalışma saatleri dışında bir zaman

        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotFound() {
        process.institutionChannel = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotActive() {
        when(process.institutionChannel.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotWorkingHours() {
        when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
        when(LocalTime.now()).thenReturn(LocalTime.of(17, 30)); // Çalışma saatleri dışında bir zaman

        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionDebtTypeNotFound() {
        process.institutionDebtType = null;
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionDebtTypeNotActive() {
        when(process.institutionDebtType.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

    @Test
    public void testBeforeExecuteProcess_Success() throws BillException {
        // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
        process.beforeExecuteProcess();
    }
}
 bu benim test sınıfım ama tümünde "java.lang.NullPointerException: Cannot assign field "processService" because "this.process" is null
" hatasını almaktayım ."

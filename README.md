 class AbstractProcessTest {

    @Mock
    private ProcessService processService;

    @Mock
    private ProcessChannelDTO processChannel;

    @Mock
    private InstitutionDTO institution;

    @Mock
    private InstitutionDebtTypeDTO institutionDebtType;

    @Mock
    private InstitutionChannelDTO institutionChannel;

    @Mock
    private InstitutionProcessDTO institutionProcess;

    @Mock
    private InstitutionChannelProcessDTO institutionChannelProcess;

    @Mock
    private ProcessLogDTO logDTO;

    @InjectMocks
    private AbstractProcess process = new NotifyPaymentProcess(); // Concrete class implementation

    @BeforeEach
    public void setUp() {
        // Varsayılan mock davranışlarını ayarla
        when(processChannel.getIsActive()).thenReturn(true);
        when(institution.getIsActive()).thenReturn(true);
        when(institutionProcess.getIsActive()).thenReturn(true);
        when(institutionChannel.getIsActive()).thenReturn(true);
        when(institutionChannelProcess.getIsActive()).thenReturn(true);
        when(institutionDebtType.getIsActive()).thenReturn(true);

        process.processChannel = processChannel;
        process.institution = institution;
        process.institutionDebtType = institutionDebtType;
        process.institutionChannel = institutionChannel;
        process.institutionProcess = institutionProcess;
        process.institutionChannelProcess = institutionChannelProcess;
        process.channelCode = "channelCode";
        process.institutionDebtTypeId = 1L;
    }

    @Test
    public void testBeforeExecuteProcess_ProcessChannelNotFound() throws BillException {
        process.processChannel = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_ProcessChannelNotActive() throws BillException {
        when(processChannel.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionNotFound() throws BillException {
        process.institution = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionNotActive() throws BillException {
        when(institution.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionProcessNotFound() throws BillException {
        process.institutionProcess = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionProcessNotActive() throws BillException {
        when(institutionProcess.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotFound() throws BillException {
        process.institutionChannelProcess = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotActive() throws BillException {
        when(institutionChannelProcess.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotFound() throws BillException {
        process.institutionChannel = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotActive() throws BillException {
        when(institutionChannel.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionDebtTypeNotFound() throws BillException {
        process.institutionDebtType = null;

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_InstitutionDebtTypeNotActive() throws BillException {
        when(institutionDebtType.getIsActive()).thenReturn(false);

        process.beforeExecuteProcess();
    }

    @Test
    public void testBeforeExecuteProcess_Success() throws BillException {
        process.beforeExecuteProcess();

        // Eğer exception fırlatılmadıysa, başarılı sayılabilir
    }
    @Test
    public void testAfterExecuteProcess_Success() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.error = EnumBillResult.SUCCESS;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.SUCCESS.getCode().toString(), logDTO.getResultCode());
        assertEquals(EnumBillResult.SUCCESS.getExplanation(), logDTO.getResultText());
        assertEquals(EnumLoggingResultType.SUCCESS.getExplanation(), logDTO.getReturnType());
    }

    @Test
    public void testAfterExecuteProcess_ErrorAndRaiseException() throws BillException {
        process.error = EnumBillResult.SUCCESS;
        process.shouldRaiseExceptionOnABillError = true;

        process.afterExecuteProcess();
    }

    @Test
    public void testAfterExecuteProcess_ErrorNoRaiseException() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.error = EnumBillResult.SUCCESS;
        process.shouldRaiseExceptionOnABillError = false;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.SUCCESS.getCode().toString(), logDTO.getResultCode());
        assertEquals(EnumBillResult.SUCCESS.getExplanation(), logDTO.getResultText());
        assertEquals(EnumLoggingResultType.ERROR.getExplanation(), logDTO.getReturnType());
    }

}


java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.ProcessChannelDTO.getIsActive()" because "this.processChannel" is null


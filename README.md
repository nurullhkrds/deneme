@RunWith(MockitoJUnitRunner.class)
public class AbstractProcessTest {

    @Mock
    private ProcessService processService;

    @Mock
    private ProcessLogDTO logDTO;

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

    @InjectMocks
    private AbstractProcess process = new NotifyPaymentProcess(); // Concrete class implementation needed

    @Before
    public void setUp() {
        // Initialize common mocks and default behavior
        when(processService.getProcessChannel(anyString(), anyString())).thenReturn(processChannel);
        when(processService.getInstitutionForProcess(anyString(), anyString())).thenReturn(institution);
        when(processService.getInstitutionDebtTypeForProcess(anyString(), anyString(), anyLong())).thenReturn(institutionDebtType);
        when(processService.getInstitutionChannelForProcess(anyLong(), anyString())).thenReturn(institutionChannel);
        when(processService.getInstitutionProcess(anyString(), anyString(), anyString())).thenReturn(institutionProcess);
        when(processService.getInstitutionChannelProcess(anyLong(), anyString(), anyString())).thenReturn(institutionChannelProcess);

        // Default behaviors
        when(processChannel.getIsActive()).thenReturn(true);
        when(institution.getIsActive()).thenReturn(true);
        when(institutionProcess.getIsActive()).thenReturn(true);
        when(institutionChannel.getIsActive()).thenReturn(true);
        when(institutionChannelProcess.getIsActive()).thenReturn(true);
        when(institutionDebtType.getIsActive()).thenReturn(true);
    }

    @Test
    public void testBeforeExecuteProcess_Success() throws BillException {
        process.beforeExecuteProcess();

        verify(logDTO, atLeastOnce()).appendProcessLog(anyString());
    }

    @Test(expected = BillException.class)
    public void testBeforeExecuteProcess_ProcessChannelNotFound() throws BillException {
        when(processChannel).thenReturn(null);

        process.beforeExecuteProcess();
    }

    @Test(expected = BillException.class)
    public void testBeforeExecuteProcess_InstitutionNotFound() throws BillException {
        when(institution).thenReturn(null);

        process.beforeExecuteProcess();
    }

    // Add more tests to cover each condition check in beforeExecuteProcess

    @Test
    public void testAfterExecuteProcess_Success() throws BillException {
        process.afterExecuteProcess();

        assertEquals(EnumBillResult.SUCCESS.getCode().toString(), logDTO.getResultCode());
    }

    @Test
    public void testInitProcess() {
        ProcessExecutionInput input = new ProcessExecutionInput();
        input.setProcessCode(EnumProcessCode.SOME_CODE);
        input.setProductCode("productCode");
        input.setInstitutionCode("institutionCode");
        input.setChannelSessionId("sessionId");
        input.setChannelTransactionId("transactionId");
        input.setChannelCode("channelCode");
        input.setAgentCode("agentCode");
        input.setBranchCode("branchCode");
        input.setInstitutionDebtTypeId(1L);
        input.setDataPack(new HashMap<>());

        process.initProcess(input, logDTO);

        verify(processService).getProcessChannel("SOME_CODE", "channelCode");
        verify(processService).getInstitutionForProcess("productCode", "institutionCode");
        // Verify other service calls
    }

    @Test
    public void testExecuteSteps_NoErrors() throws BillException {
        // Add steps to process
        ProcessStep step1 = mock(ProcessStep.class);
        ProcessStep step2 = mock(ProcessStep.class);

        process.addProcessStep(step1);
        process.addProcessStep(step2);

        process.executeSteps();

        verify(step1).executeStep();
        verify(step2).executeStep();
    }

    @Test
    public void testExecuteSteps_WithError() throws BillException {
        process.error = EnumBillResult.SOME_ERROR;

        ProcessStep step1 = mock(ProcessStep.class);
        process.addProcessStep(step1);

        process.executeSteps();

        verify(step1, never()).executeStep();
    }

    // Add more test cases to cover different scenarios
}

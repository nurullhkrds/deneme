 private BusinessLogDTO businessLogDTO;
    private ProcessLogDTO processLogDTO;

    @Mock
    private LoggingService mockLoggingService;

    @BeforeEach
    void setUp() {
        businessLogDTO = new BusinessLogDTO();
        processLogDTO = new ProcessLogDTO();
    }

    @Test
    void testAppendBusinessLog() {
        businessLogDTO.setLastLoggingTime(System.nanoTime());
        String logText = "Test Log";

        LogUtil.appendBusinessLog(businessLogDTO, logText);

        assertNotNull(businessLogDTO.getRequestData());
        assertTrue(businessLogDTO.getRequestData().contains(logText));
    }

    @Test
    void testSaveBusinessLog() {
        // Instead of mocking static methods, we directly interact with mockLoggingService
        SpringUtil.setBean(LoggingService.class, mockLoggingService);  // Hypothetical method to inject dependency
        
        LogUtil.saveBusinessLog(businessLogDTO);

        verify(mockLoggingService, times(1)).saveBusinessLog(businessLogDTO);
    }

    @Test
    void testAppendProcessLog() {
        processLogDTO.setLastLoggingTime(System.currentTimeMillis());
        String logText = "Process Log";

        LogUtil.appendProcessLog(processLogDTO, logText);

        assertNotNull(processLogDTO.getResponseData1());
        assertTrue(processLogDTO.getResponseData1().contains(logText));
    }

    @Test
    void testSaveProcessLog() {
        // Instead of mocking static methods, we directly interact with mockLoggingService
        SpringUtil.setBean(LoggingService.class, mockLoggingService);  // Hypothetical method to inject dependency
        
        String processInput = "Process Input";
        LogUtil.saveProcessLog(processLogDTO, processInput);

        assertEquals(processInput, processLogDTO.getRequestData());
        verify(mockLoggingService, times(1)).saveProcessLog(processLogDTO);
    }

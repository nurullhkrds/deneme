@ExtendWith(MockitoExtension.class)
public class LogUtilTest {

    private BusinessLogDTO businessLogDTO;
    private ProcessLogDTO processLogDTO;

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
        try (MockedStatic<SpringUtil> mockedSpringUtil = mockStatic(SpringUtil.class)) {
            LoggingService mockLoggingService = mock(LoggingService.class);
            mockedSpringUtil.when(() -> SpringUtil.getBean(LoggingService.class)).thenReturn(mockLoggingService);

            LogUtil.saveBusinessLog(businessLogDTO);

            verify(mockLoggingService, times(1)).saveBusinessLog(businessLogDTO);
        }
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
        try (MockedStatic<SpringUtil> mockedSpringUtil = mockStatic(SpringUtil.class)) {
            LoggingService mockLoggingService = mock(LoggingService.class);
            mockedSpringUtil.when(() -> SpringUtil.getBean(LoggingService.class)).thenReturn(mockLoggingService);

            String processInput = "Process Input";
            LogUtil.saveProcessLog(processLogDTO, processInput);

            assertEquals(processInput, processLogDTO.getRequestData());
            verify(mockLoggingService, times(1)).saveProcessLog(processLogDTO);
        }
    }
}

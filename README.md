@ExtendWith(MockitoExtension.class)
public class LoggingServiceImplTest {

    @Mock
    private BusinessLoggingService businessLogService;

    @Mock
    private ServiceLoggingService serviceLogService;

    @Mock
    private ProcessLoggingService processLogService;

    @InjectMocks
    private LoggingServiceImpl loggingService;

    private BusinessLogDTO businessLogDTO;
    private ServiceLogDTO serviceLogDTO;
    private ProcessLogDTO processLogDTO;

    @BeforeEach
    void setUp() {
        businessLogDTO = new BusinessLogDTO();
        serviceLogDTO = new ServiceLogDTO();
        processLogDTO = new ProcessLogDTO();
    }

    @Test
    void testSaveBusinessLog() {
        // Act
        loggingService.saveBusinessLog(businessLogDTO);

        // Assert
        verify(businessLogService, times(1)).saveBusinessLog(businessLogDTO);
    }

    @Test
    void testSaveServiceLog() {
        // Act
        loggingService.saveServiceLog(serviceLogDTO);

        // Assert
        verify(serviceLogService, times(1)).saveServiceLog(serviceLogDTO);
    }

    @Test
    void testSaveProcessLog() {
        // Act
        loggingService.saveProcessLog(processLogDTO);

        // Assert
        verify(processLogService, times(1)).saveProcessLog(processLogDTO);
    }
}

@ExtendWith(MockitoExtension.class)
public class ProcessLoggingServiceImplTest {

    @Mock
    private ProcessLogRepository repo;

    @Mock
    private ProcessLogMapper mapper;

    @InjectMocks
    private ProcessLoggingServiceImpl service;

    private ProcessLogDTO processLogDTO;

    @BeforeEach
    void setUp() {
        processLogDTO = new ProcessLogDTO();
        processLogDTO.setStartTime(System.currentTimeMillis());
        processLogDTO.setFinishTime(System.currentTimeMillis() + 1000);
        processLogDTO.setRequestData("Sample request data");
        processLogDTO.setResponseData1("Sample response data that is too long and should be truncated.");
    }

    @Test
    void testSaveProcessLog_withValidData() {
        // Arrange
        ProcessLog processLogEntity = new ProcessLog();
        when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
        when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

        // Act
        service.saveProcessLog(processLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(ProcessLogDTO.class));
        verify(repo, times(1)).save(any(ProcessLog.class));
        assertNotNull(processLogDTO.getElapsedTime());
        assertTrue(processLogDTO.getRequestData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertTrue(processLogDTO.getResponseData1().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertTrue(processLogDTO.getResponseData2().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
    }

    @Test
    void testSaveProcessLog_withException() {
        // Arrange
        ExceptionData exceptionData = new ExceptionData(1001L, "Error message");
        MicroException microException = mock(MicroException.class);
        when(microException.getExceptionData()).thenReturn(exceptionData);
        processLogDTO.setException(microException);

        ProcessLog processLogEntity = new ProcessLog();
        when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
        when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

        // Act
        service.saveProcessLog(processLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(ProcessLogDTO.class));
        verify(repo, times(1)).save(any(ProcessLog.class));
        assertEquals("1001", processLogDTO.getResultCode());
        assertEquals("Error message", processLogDTO.getResultText());
    }

    @Test
    void testSaveProcessLog_withUnknownException() {
        // Arrange
        Exception exception = new Exception("Unknown error");
        processLogDTO.setException(exception);

        ProcessLog processLogEntity = new ProcessLog();
        when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
        when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

        // Act
        service.saveProcessLog(processLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(ProcessLogDTO.class));
        verify(repo, times(1)).save(any(ProcessLog.class));
        assertEquals(LoggingConstants.UNKNOWN_ERROR_CODE.toString(), processLogDTO.getResultCode());
        assertEquals(LoggingConstants.UNKNOWN_ERROR_MESSAGE, processLogDTO.getResultText());
    }

    @Test
    void testSaveProcessLog_withNullProcessLog() {
        // Act
        service.saveProcessLog(null);

        // Assert
        verify(repo, never()).save(any());
        verify(mapper, never()).toEntity(any());
    }
}

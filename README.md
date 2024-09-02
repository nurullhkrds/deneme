@ExtendWith(MockitoExtension.class)
public class ServiceLoggingServiceImplTest {

    @Mock
    private ServiceLogRepository repo;

    @Mock
    private ServiceLogMapper mapper;

    @InjectMocks
    private ServiceLoggingServiceImpl service;

    private ServiceLogDTO serviceLogDTO;

    @BeforeEach
    void setUp() {
        serviceLogDTO = new ServiceLogDTO();
        serviceLogDTO.setRequestData("Sample request data");
        serviceLogDTO.setResponseData("Sample response data");
    }

    @Test
    void testSaveServiceLog_withValidData() {
        // Arrange
        ServiceLog serviceLogEntity = new ServiceLog();
        when(mapper.toEntity(any(ServiceLogDTO.class))).thenReturn(serviceLogEntity);
        when(repo.save(any(ServiceLog.class))).thenReturn(serviceLogEntity);

        // Act
        service.saveServiceLog(serviceLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(ServiceLogDTO.class));
        verify(repo, times(1)).save(any(ServiceLog.class));
        assertTrue(serviceLogDTO.getRequestData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertTrue(serviceLogDTO.getResponseData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
    }

    @Test
    void testSaveServiceLog_withLongRequestData() {
        // Arrange
        String longRequestData = "a".repeat(LoggingConstants.MAX_LOGGING_LENGHT + 10);
        serviceLogDTO.setRequestData(longRequestData);

        ServiceLog serviceLogEntity = new ServiceLog();
        when(mapper.toEntity(any(ServiceLogDTO.class))).thenReturn(serviceLogEntity);
        when(repo.save(any(ServiceLog.class))).thenReturn(serviceLogEntity);

        // Act
        service.saveServiceLog(serviceLogDTO);

        // Assert
        assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, serviceLogDTO.getRequestData().length());
    }

    @Test
    void testSaveServiceLog_withLongResponseData() {
        // Arrange
        String longResponseData = "a".repeat(LoggingConstants.MAX_LOGGING_LENGHT + 10);
        serviceLogDTO.setResponseData(longResponseData);

        ServiceLog serviceLogEntity = new ServiceLog();
        when(mapper.toEntity(any(ServiceLogDTO.class))).thenReturn(serviceLogEntity);
        when(repo.save(any(ServiceLog.class))).thenReturn(serviceLogEntity);

        // Act
        service.saveServiceLog(serviceLogDTO);

        // Assert
        assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, serviceLogDTO.getResponseData().length());
    }

    @Test
    void testSaveServiceLog_withException() {
        // Arrange
        ExceptionData exceptionData = new ExceptionData(1001L, "Error message");
        MicroException microException = mock(MicroException.class);
        when(microException.getExceptionData()).thenReturn(exceptionData);
        serviceLogDTO.setException(microException);

        ServiceLog serviceLogEntity = new ServiceLog();
        when(mapper.toEntity(any(ServiceLogDTO.class))).thenReturn(serviceLogEntity);
        when(repo.save(any(ServiceLog.class))).thenReturn(serviceLogEntity);

        // Act
        service.saveServiceLog(serviceLogDTO);

        // Assert
        assertEquals("1001", serviceLogDTO.getResultCode());
        assertTrue(serviceLogDTO.getResponseData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertEquals(EnumLoggingResultType.ERROR, serviceLogDTO.getResultType());
    }

    @Test
    void testSaveServiceLog_withUnknownException() {
        // Arrange
        Exception exception = new Exception("Unknown error");
        serviceLogDTO.setException(exception);

        ServiceLog serviceLogEntity = new ServiceLog();
        when(mapper.toEntity(any(ServiceLogDTO.class))).thenReturn(serviceLogEntity);
        when(repo.save(any(ServiceLog.class))).thenReturn(serviceLogEntity);

        // Act
        service.saveServiceLog(serviceLogDTO);

        // Assert
        assertEquals(LoggingConstants.UNKNOWN_ERROR_CODE.toString(), serviceLogDTO.getResultCode());
        assertTrue(serviceLogDTO.getResponseData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertEquals(EnumLoggingResultType.ERROR, serviceLogDTO.getResultType());
    }

    @Test
    void testSaveServiceLog_withNullServiceLog() {
        // Act
        service.saveServiceLog(null);

        // Assert
        verify(repo, never()).save(any());
        verify(mapper, never()).toEntity(any());
    }
}

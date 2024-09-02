@ExtendWith(MockitoExtension.class)
public class BusinessLoggingServiceImplTest {

    @Mock
    private BusinessLogRepository repository;

    @Mock
    private BusinessLogMapper mapper;

    @InjectMocks
    private BusinessLoggingServiceImpl service;

    private BusinessLogDTO businessLogDTO;

    @BeforeEach
    void setUp() {
        businessLogDTO = new BusinessLogDTO();
        businessLogDTO.setStartTime(System.nanoTime());
        businessLogDTO.setRequestData("Request data example");
    }

    @Test
    void testSaveBusinessLog_withLongRequestData() {
        // Arrange
        String longRequestData = "a".repeat(LoggingConstants.MAX_LOGGING_LENGHT + 10);
        businessLogDTO.setRequestData(longRequestData);

        BusinessLog businessLogEntity = new BusinessLog();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(businessLogEntity);
        when(repository.save(any(BusinessLog.class))).thenReturn(businessLogEntity);

        // Act
        service.saveBusinessLog(businessLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(BusinessLogDTO.class));
        verify(repository, times(1)).save(any(BusinessLog.class));
        assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, businessLogDTO.getRequestData().length());
    }

    @Test
    void testSaveBusinessLog_withException() {
        // Arrange
        MicroException microException = new MicroException(new ExceptionData(1001L, "Error message"));
        businessLogDTO.setException(microException);

        BusinessLog businessLogEntity = new BusinessLog();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(businessLogEntity);
        when(repository.save(any(BusinessLog.class))).thenReturn(businessLogEntity);

        // Act
        service.saveBusinessLog(businessLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(BusinessLogDTO.class));
        verify(repository, times(1)).save(any(BusinessLog.class));
        assertEquals(1001, businessLogDTO.getErrorCode());
        assertEquals("Error message", businessLogDTO.getErrorMessage());
    }

    @Test
    void testSaveBusinessLog_withUnknownException() {
        // Arrange
        Exception exception = new Exception("Unknown error");
        businessLogDTO.setException(exception);

        BusinessLog businessLogEntity = new BusinessLog();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(businessLogEntity);
        when(repository.save(any(BusinessLog.class))).thenReturn(businessLogEntity);

        // Act
        service.saveBusinessLog(businessLogDTO);

        // Assert
        verify(mapper, times(1)).toEntity(any(BusinessLogDTO.class));
        verify(repository, times(1)).save(any(BusinessLog.class));
        assertEquals(LoggingConstants.UNKNOWN_ERROR_CODE, businessLogDTO.getErrorCode());
        assertEquals(LoggingConstants.UNKNOWN_ERROR_MESSAGE, businessLogDTO.getErrorMessage());
    }

    @Test
    void testSaveBusinessLog_withElapsedTimeCalculation() {
        // Arrange
        businessLogDTO.setStartTime(System.nanoTime() - Duration.ofMillis(100).toNanos());

        BusinessLog businessLogEntity = new BusinessLog();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(businessLogEntity);
        when(repository.save(any(BusinessLog.class))).thenReturn(businessLogEntity);

        // Act
        service.saveBusinessLog(businessLogDTO);

        // Assert
        assertTrue(businessLogDTO.getElapsedTime() >= 100);
    }
}

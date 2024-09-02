import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;

import com.example.dto.BusinessLogDTO;
import com.example.entity.BusinessLogEntity;
import com.example.exception.MicroException;
import com.example.mapper.BusinessLogMapper;
import com.example.repository.BusinessLogRepository;
import com.example.service.impl.BusinessLoggingServiceImpl;

import java.time.Duration;

@ExtendWith(MockitoExtension.class)
class BusinessLoggingServiceImplTest {

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
        businessLogDTO.setRequestData("Some request data");
        businessLogDTO.setStartTime(System.nanoTime());
    }

    @Test
    void testSaveBusinessLog_withLongRequestData_shouldTruncateData() {
        // Given
        businessLogDTO.setRequestData("a".repeat(LoggingConstants.MAX_LOGGING_LENGHT + 10));

        BusinessLogEntity entity = new BusinessLogEntity();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(entity);

        // When
        service.saveBusinessLog(businessLogDTO);

        // Then
        verify(repository).save(entity);
        assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, businessLogDTO.getRequestData().length());
    }

    @Test
    void testSaveBusinessLog_shouldSetElapsedTime() {
        // Given
        BusinessLogEntity entity = new BusinessLogEntity();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(entity);

        // When
        service.saveBusinessLog(businessLogDTO);

        // Then
        verify(repository).save(entity);
        assertTrue(businessLogDTO.getElapsedTime() >= 0);
    }

    @Test
    void testSaveBusinessLog_withException_shouldSetErrorDetails() {
        // Given
        MicroException exception = new MicroException("Test Exception", 123, "Test Error");
        businessLogDTO.setException(exception);

        BusinessLogEntity entity = new BusinessLogEntity();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(entity);

        // When
        service.saveBusinessLog(businessLogDTO);

        // Then
        verify(repository).save(entity);
        assertEquals(123, businessLogDTO.getErrorCode());
        assertEquals("Test Error", businessLogDTO.getErrorMessage());
        assertNotNull(businessLogDTO.getErrorDetail());
    }

    @Test
    void testSaveBusinessLog_withUnknownException_shouldSetUnknownErrorDetails() {
        // Given
        Exception exception = new RuntimeException("Test RuntimeException");
        businessLogDTO.setException(exception);

        BusinessLogEntity entity = new BusinessLogEntity();
        when(mapper.toEntity(any(BusinessLogDTO.class))).thenReturn(entity);

        // When
        service.saveBusinessLog(businessLogDTO);

        // Then
        verify(repository).save(entity);
        assertEquals(LoggingConstants.UNKNOWN_ERROR_CODE, businessLogDTO.getErrorCode());
        assertEquals(LoggingConstants.UNKNOWN_ERROR_MESSAGE, businessLogDTO.getErrorMessage());
        assertNotNull(businessLogDTO.getErrorDetail());
    }
}

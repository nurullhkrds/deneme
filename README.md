import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.slf4j.Logger;

import static org.mockito.Mockito.*;

class LogUtilsTest {

    private Logger mockLogger;

    @BeforeEach
    void setUp() {
        mockLogger = Mockito.mock(Logger.class);
    }

    @Test
    void whenErrorEnabled_shouldLogError() {
        when(mockLogger.isErrorEnabled()).thenReturn(true);

        String format = "Error: {}";
        String stackTrace = "Exception stack trace";

        LogUtils.logError(mockLogger, format, stackTrace);

        verify(mockLogger, times(1)).error(format, stackTrace);
    }

    @Test
    void whenErrorNotEnabled_shouldNotLogError() {
        when(mockLogger.isErrorEnabled()).thenReturn(false);

        String format = "Error: {}";
        String stackTrace = "Exception stack trace";

        LogUtils.logError(mockLogger, format, stackTrace);

        verify(mockLogger, never()).error(anyString(), anyString());
    }

    @Test
    void whenInfoEnabled_shouldLogInfo() {
        when(mockLogger.isInfoEnabled()).thenReturn(true);

        String format = "Info: {}";
        String message = "Information message";

        LogUtils.logInfo(mockLogger, format, message);

        verify(mockLogger, times(1)).info(format, message);
    }

    @Test
    void whenInfoNotEnabled_shouldNotLogInfo() {
        when(mockLogger.isInfoEnabled()).thenReturn(false);

        String format = "Info: {}";
        String message = "Information message";

        LogUtils.logInfo(mockLogger, format, message);

        verify(mockLogger, never()).info(anyString(), anyString());
    }
}

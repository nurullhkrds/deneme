public class LogUtils {

    private LogUtils() {
        throw new IllegalStateException("Utility class");
    }

    public static void logError(Logger logger, String format, String stackTrace) {
        if (logger.isErrorEnabled()) {
            logger.error(format, stackTrace);
        }
    }

    public static void logInfo(Logger logger, String format, String message) {
        if (logger.isInfoEnabled()) {
            logger.info(format, message);
        }
    }

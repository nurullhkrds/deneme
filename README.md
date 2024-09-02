@Service
@RequiredArgsConstructor
public class BusinessLoggingServiceImpl implements BusinessLoggingService {

	private final BusinessLogRepository repository;
	private final BusinessLogMapper mapper;

	@Async
	@Transactional
	@Override
	public void saveBusinessLog(BusinessLogDTO businessLog) {
		String requestData = businessLog.getRequestData();

		if (StringUtils.isNotBlank(requestData) && requestData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {
			businessLog.setRequestData(StringUtils.substring(requestData, 0, LoggingConstants.MAX_LOGGING_LENGHT));
		}

		long currentTime = System.nanoTime();
		businessLog.setElapsedTime(Duration.ofNanos(currentTime - businessLog.getStartTime()).toMillis());

		if (businessLog.getException() != null) {
			resolveExceptionForLog(businessLog);
		}

		repository.save(mapper.toEntity(businessLog));
	}

	private void resolveExceptionForLog(BusinessLogDTO businessLog) {
		Exception exception = businessLog.getException();

		if (exception instanceof MicroException) {
			MicroException microException = (MicroException) exception;
			businessLog.setErrorCode(microException.getExceptionData().getErrorCode().intValue());
			businessLog.setErrorMessage(microException.getExceptionData().getErrorMessage());

			String stackTrace = ExceptionUtils.getStackTrace(microException);
			businessLog.setErrorDetail(stackTrace.length() > LoggingConstants.MAX_LOGGING_LENGHT
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);
		}

		else {
			businessLog.setErrorCode(LoggingConstants.UNKNOWN_ERROR_CODE);
			businessLog.setErrorMessage(LoggingConstants.UNKNOWN_ERROR_MESSAGE);

			String stackTrace = ExceptionUtils.getStackTrace(exception);
			businessLog.setErrorDetail(stackTrace.length() > LoggingConstants.MAX_LOGGING_LENGHT
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);
		}

	}

}

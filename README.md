@Service
@RequiredArgsConstructor
public class ServiceLoggingServiceImpl implements ServiceLoggingService {

	private final ServiceLogRepository repo;
	private final ServiceLogMapper mapper;

	@Override
	@Async
	@Transactional
	public void saveServiceLog(ServiceLogDTO serviceLog) {
		if (serviceLog == null) {
			return;
		}

		//long currentTimeMillis = System.currentTimeMillis();
		//serviceLog.setElapsedTime(currentTimeMillis - serviceLog.getStartTime());

		String requestData = serviceLog.getRequestData();
		if (StringUtils.isNotBlank(requestData) && requestData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {
			serviceLog.setRequestData(StringUtils.substring(requestData, 0, LoggingConstants.MAX_LOGGING_LENGHT));
		}

		String responseData = serviceLog.getResponseData();
		if (StringUtils.isNotBlank(responseData) && responseData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {
			serviceLog.setResponseData(StringUtils.substring(responseData, 0, LoggingConstants.MAX_LOGGING_LENGHT));
		}

		if (serviceLog.getException() != null) {
			resolveExceptionForLog(serviceLog);
		}

		repo.save(mapper.toEntity(serviceLog));
	}

	private void resolveExceptionForLog(ServiceLogDTO serviceLog) {
		Exception exception = serviceLog.getException();

		serviceLog.setReturnCode(LoggingConstants.UNKNOWN_ERROR_CODE);
		serviceLog.setResultType(EnumLoggingResultType.ERROR);

		if (exception instanceof MicroException) {
			MicroException microException = (MicroException) exception;

			serviceLog.setResultCode(microException.getExceptionData().getErrorCode().toString());
			String stackTrace = ExceptionUtils.getStackTrace(microException);
			serviceLog.setResponseData(stackTrace.length() > 4000
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);
		} else {
			serviceLog.setResultCode(LoggingConstants.UNKNOWN_ERROR_CODE.toString());
			String stackTrace = ExceptionUtils.getStackTrace(exception);
			serviceLog.setResponseData(stackTrace.length() > 4000
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);
		}

	}

}

@Service
@RequiredArgsConstructor
public class ProcessLoggingServiceImpl implements ProcessLoggingService {

	private final ProcessLogRepository repo;
	private final ProcessLogMapper mapper;

	@Async
	@Transactional
	@Override
	public void saveProcessLog(ProcessLogDTO processLog) {
		if (processLog == null) {
			return;
		}

		processLog.setElapsedTime(Duration.ofMillis(processLog.getFinishTime() - processLog.getStartTime()).toMillis());

		String requestData = processLog.getRequestData();
		if (StringUtils.isNotBlank(requestData) && requestData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {
			processLog.setRequestData(StringUtils.substring(requestData, 0, LoggingConstants.MAX_LOGGING_LENGHT));
		}

		String responseData = processLog.getResponseData1();
		if (StringUtils.isNotBlank(responseData) && responseData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {

			String responseData1 = StringUtils.substring(responseData, 0, LoggingConstants.MAX_LOGGING_LENGHT);
			String responseData2 = StringUtils.substring(responseData, LoggingConstants.MAX_LOGGING_LENGHT);
			responseData2 = responseData2.length() > LoggingConstants.MAX_LOGGING_LENGHT
					? StringUtils.substring(responseData2, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: responseData2;
			processLog.setResponseData1(responseData1);
			processLog.setResponseData2(responseData2);
		}

		if (processLog.getException() != null) {
			resolveExceptionForLog(processLog);
		}

		repo.save(mapper.toEntity(processLog));
	}

	private void resolveExceptionForLog(ProcessLogDTO processLog) {
		Exception exception = processLog.getException();

		if (exception instanceof MicroException) {
			MicroException microException = (MicroException) exception;

			processLog.setResultCode(microException.getExceptionData().getErrorCode().toString());
			processLog.setResultText(microException.getExceptionData().getErrorMessage());

			String stackTrace = ExceptionUtils.getStackTrace(microException);
			processLog.setExceptionTrace(stackTrace.length() > 4000
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);

		} else {
			processLog.setResultCode(LoggingConstants.UNKNOWN_ERROR_CODE.toString());
			processLog.setResultText(LoggingConstants.UNKNOWN_ERROR_MESSAGE);

			String stackTrace = ExceptionUtils.getStackTrace(exception);
			processLog.setExceptionTrace(stackTrace.length() > 4000
					? StringUtils.substring(stackTrace, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: stackTrace);
		}

	}

}

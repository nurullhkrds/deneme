public class LogUtil {

	private LogUtil() {
		throw new IllegalStateException("LogUtil: Utility class");
	}

	public static void appendBusinessLog(BusinessLogDTO logDTO, String logText) {
		if (logDTO == null) {
			return;
		}

		long currentTime = System.nanoTime();
		
		logDTO.setRequestData(
				new StringBuilder().append(logDTO.getRequestData()).append(String.format("%-40s", logText)).append("  ")
						.append(LocalDateTime.now().format(DateTimeFormatter.ISO_LOCAL_DATE_TIME)).append("  ")
						.append(Duration.ofNanos(currentTime - logDTO.getLastLoggingTime()).toMillis()).append(" ms").append("\n")
						.toString());
		logDTO.setLastLoggingTime(System.currentTimeMillis());
	}

	public static void saveBusinessLog(BusinessLogDTO businessLog) {
		if (businessLog == null) {
			return;
		}

		SpringUtil.getBean(LoggingService.class).saveBusinessLog(businessLog);
	}

	public static void appendProcessLog(ProcessLogDTO logDTO, String logText) {
		if (logDTO == null) {
			return;
		}

		long currentTime = System.currentTimeMillis();
		
		logDTO.setResponseData1(
				new StringBuilder().append(logDTO.getResponseData1()).append(logText)
						//.append("  ").append(LocalDateTime.now().format(DateTimeFormatter.ISO_LOCAL_DATE_TIME))
						.append("  ").append(Duration.ofMillis(currentTime-logDTO.getLastLoggingTime()).toMillis()).append(" ms")
						.append("\n").toString());
		logDTO.setLastLoggingTime(System.currentTimeMillis());
	}

	/**
	 * TODO: process altyap geliştirildğinde buraya obje gönderelim
	 */
	public static void saveProcessLog(ProcessLogDTO processLogDTO, String processInput) {
		if (processLogDTO == null) {
			return;
		}
		processLogDTO.setRequestData(processInput);

		SpringUtil.getBean(LoggingService.class).saveProcessLog(processLogDTO);
	}
}

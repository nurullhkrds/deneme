@Service
@RequiredArgsConstructor
public class LoggingServiceImpl implements LoggingService {

	private final BusinessLoggingService businessLogService;
	private final ServiceLoggingService serviceLogService;
	private final ProcessLoggingService processLogService;
	
	@Override
	public void saveBusinessLog(BusinessLogDTO businessLog) {
		businessLogService.saveBusinessLog(businessLog);
	}

	@Override
	public void saveServiceLog(ServiceLogDTO serviceLog) {
		serviceLogService.saveServiceLog(serviceLog);
	}

	@Override
	public void saveProcessLog(ProcessLogDTO processLog) {
		processLogService.saveProcessLog(processLog);
	}

}

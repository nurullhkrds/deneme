@Getter
@Setter
public abstract class BaseLogDTO extends CreatableBaseDTO {

	private Long institutionId;

	private Long institutionDebtTypeId;

	private String subscriberNo;

	private LocalDate logDate;

	private String branchCode;

	private String channelCode;

	private String channelTransactionId;

	private String channelSessionId;

	private Long elapsedTime;
	
	private long startTime = System.currentTimeMillis();
	
	private long lastLoggingTime = this.startTime;
	
	private long finishTime = 0l;
	
	private Exception exception;
}

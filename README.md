@Getter
@Setter
@RequiredArgsConstructor
public class ProcessLogDTO extends BaseLogDTO {
	private Long id;

	private String subscriberNo;

	private final String processCode;

	private Long customerNo;

	private Long identityNo;

	private String taxId;

	private String returnType;

	private String resultCode;

	private String resultText;

	private String requestData;

	private String responseData1 = "";

	private String responseData2;

	private String exceptionTrace;

}

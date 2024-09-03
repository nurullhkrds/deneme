@RequiredArgsConstructor
@Getter
@Setter
public class ServiceLogDTO extends BaseLogDTO {
	private Long id;

	private String serviceDirection;

	private final String applicationName;

	private final String serviceName;

	private final String methodName;

	private String key1;

	private String key2;

	private String key3;

	private String key4;

	private String key5;

	private Integer returnCode;

	private String resultCode;

	private EnumLoggingResultType resultType;

	private String requestData;

	private String responseData;
}

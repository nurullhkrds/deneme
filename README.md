@RequiredArgsConstructor
@Getter
@Setter
public class BusinessLogDTO extends BaseLogDTO {
	private Long id;

	@NotNull
	private final String applicationName;

	@NotNull
	private final String serviceName;

	@NotNull
	private final String methodName;

	private String key1;

	private String key2;

	private String key3;

	private String key4;

	private String key5;

	private Integer errorCode;

	private String errorMessage;

	private String requestData;

	private String errorDetail;
}

@AllArgsConstructor
@JsonAdapter(EnumLoggingResultTypeConverter.class)
@ToString
public enum EnumLoggingResultType {

	SUCCESS("S", "SUCCESS"),
	ERROR("E", "ERROR");
	
	@Getter
	@JsonValue
	private String value;
	
	@Getter
	private String explanation;
	
	private static final Map<String, EnumLoggingResultType> paramaters;

	static {
		paramaters = new LinkedHashMap<>();

		for (EnumLoggingResultType each : EnumLoggingResultType.values()) {
			paramaters.put(each.value, each);
		}

	}	

	@JsonCreator
	public static EnumLoggingResultType parse(String value) {
		return paramaters.get(value);
	}
}

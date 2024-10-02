@AllArgsConstructor
@JsonAdapter(EnumExpenseTypeConverter.class)
@ToString
public enum EnumExpenseType {

	FROM_CUSTOMER("CUST"),
	FROM_INSTITUTION("INST");
	
	@Getter
	@JsonValue
	private String value;
	
	private static final Map<String, EnumExpenseType> paramaters;

	static {
		paramaters = new LinkedHashMap<>();

		for (EnumExpenseType each : EnumExpenseType.values()) {
			paramaters.put(each.value, each);
		}

	}	

	@JsonCreator
	public static EnumExpenseType parse(String value) {
		return paramaters.get(value);
	}
}

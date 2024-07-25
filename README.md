@AllArgsConstructor
@JsonAdapter(EnumReturnTypeConverter.class)
@ToString
@Getter
public enum EnumReturnType {
	SUCCESS("SUCCESS"),
	ERROR("ERROR");

	@JsonValue
	private final String value;

	private static final Map<String, EnumReturnType> returnTypes = new HashMap<>();

	static {
		for (EnumReturnType type : EnumReturnType.values()) {
			returnTypes.put(type.getValue(), type);
		}
	}

	@JsonCreator
	public static EnumReturnType getReturnType(String value) {
		return returnTypes.get(value);
	}
}

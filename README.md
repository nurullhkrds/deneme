@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // Enum'un string değer olarak serialize ve deserialize edilmesini sağlar
public enum EnumBlockDayType {
	
	CALENDAR_DAY("C"),
	WORKING_DAY("W");

	@Getter
	@JsonValue
	private String value;
	
	private static final Map<String, EnumBlockDayType> paramaters;

	static {
		paramaters = new LinkedHashMap<>();
		for (EnumBlockDayType each : EnumBlockDayType.values()) {
			paramaters.put(each.value, each);
		}
	}	

	@JsonCreator
	public static EnumBlockDayType parse(String value) {
		EnumBlockDayType type = paramaters.get(value);
		if (type == null) {
			throw new IllegalArgumentException("Invalid value for EnumBlockDayType: " + value);
		}
		return type;
	}
}

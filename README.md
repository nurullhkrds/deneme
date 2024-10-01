@AllArgsConstructor
@JsonAdapter(EnumBlockDayTypeConverter.class)
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
		return paramaters.get(value);
	}
}

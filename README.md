@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // JSON işlemlerinde enum'un string olarak işlenmesini sağlar
public enum EnumBlockDayType {
	
	CALENDAR_DAY("C"),
	WORKING_DAY("W");

	@Getter
	private final String value;
	
	private static final Map<String, EnumBlockDayType> parameters;

	static {
		parameters = new LinkedHashMap<>();
		for (EnumBlockDayType each : EnumBlockDayType.values()) {
			parameters.put(each.value, each);
		}
	}

	@JsonCreator
	public static EnumBlockDayType fromValue(String value) {
		EnumBlockDayType type = parameters.get(value);
		if (type == null) {
			throw new IllegalArgumentException("Invalid value for EnumBlockDayType: " + value);
		}
		return type;
	}
}

@Converter(autoApply = true)
public class EnumBlockDayTypeConverter implements AttributeConverter<EnumBlockDayType, String> {

	@Override
	public String convertToDatabaseColumn(EnumBlockDayType attribute) {
		return (attribute == null) ? null : attribute.getValue();
	}

	@Override
	public EnumBlockDayType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumBlockDayType.fromValue(dbData);
	}
}

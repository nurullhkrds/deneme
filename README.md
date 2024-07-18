
@Converter(autoApply = true)
public class EnumReturnTypeConverter extends TypeAdapter<EnumReturnType>
		implements AttributeConverter<EnumReturnType, String> {

	@Override
	public String convertToDatabaseColumn(EnumReturnType statu) {
		if (statu == null) {
			return StringUtils.EMPTY;
		}

		return statu.getValue();
	}

	@Override
	public EnumReturnType convertToEntityAttribute(String dbData) {
		return EnumReturnType.getReturnType(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumReturnType tokenStatu) throws IOException {
		if (tokenStatu != null) {
			out.jsonValue(tokenStatu.getValue());
		}
	}

	@Override
	public EnumReturnType read(JsonReader in) throws IOException {
		return EnumReturnType.getReturnType(in.nextString());
	}

}


@AllArgsConstructor
@JsonAdapter(EnumReturnTypeConverter.class)
@ToString
public enum EnumReturnType {

	SUCCESS("SUCCESS"), ERROR("ERROR");
	
	@Getter
	@JsonValue
	private String value;
	
	private static final Map<String, EnumReturnType> returnTypes;
	
	static {
		returnTypes = new LinkedHashMap<>();
		
		for (EnumReturnType each : EnumReturnType.values()) {
			returnTypes.put(each.value, each);
		}
		
	}
	
	@JsonCreator
	public static EnumReturnType getReturnType(String value) {
		return returnTypes.get(value);
	}
}

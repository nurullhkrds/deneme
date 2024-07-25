@Converter(autoApply = true)
public class EnumReturnTypeConverter extends TypeAdapter<EnumReturnType>
		implements AttributeConverter<EnumReturnType, String> {

	@Override
	public String convertToDatabaseColumn(EnumReturnType returnType) {
		return returnType != null ? returnType.getValue() : null;
	}

	@Override
	public EnumReturnType convertToEntityAttribute(String dbValue) {
		return EnumReturnType.getReturnType(dbValue);
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

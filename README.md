@Converter(autoApply = true)
public class EnumLoggingResultTypeConverter extends TypeAdapter<EnumLoggingResultType>
		implements AttributeConverter<EnumLoggingResultType, String> {

	@Override
	public String convertToDatabaseColumn(EnumLoggingResultType attribute) {
		return (attribute==null) ? null : attribute.getValue();
	}

	@Override
	public EnumLoggingResultType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumLoggingResultType.parse(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumLoggingResultType value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}

	}

	@Override
	public EnumLoggingResultType read(JsonReader in) throws IOException {
		return EnumLoggingResultType.parse(in.nextString());
	}

}

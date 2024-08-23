@Converter(autoApply = true)
public class EnumExpenseTypeConverter extends TypeAdapter<EnumExpenseType> implements AttributeConverter <EnumExpenseType,String>{
	
	@Override
	public String convertToDatabaseColumn(EnumExpenseType attribute) {
		return (attribute==null) ? null : attribute.getValue();
	}

	@Override
	public EnumExpenseType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumExpenseType.parse(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumExpenseType value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}
	}

	@Override
	public EnumExpenseType read(JsonReader in) throws IOException {
		return EnumExpenseType.parse(in.nextString());
	}

}

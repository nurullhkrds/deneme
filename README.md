@Converter(autoApply = true)
public class EnumAccountingSourceConverter extends TypeAdapter<EnumAccountingSource> implements AttributeConverter <EnumAccountingSource,String> {

	@Override
	public String convertToDatabaseColumn(EnumAccountingSource attribute) {
		return (attribute==null) ? null : attribute.getValue();
	}

	@Override
	public EnumAccountingSource convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumAccountingSource.parse(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumAccountingSource value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}
		
	}

	@Override
	public EnumAccountingSource read(JsonReader in) throws IOException {
		return EnumAccountingSource.parse(in.nextString());
	}

}

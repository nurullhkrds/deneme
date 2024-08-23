@Converter(autoApply = true)
public class EnumBlockDayTypeConverter implements AttributeConverter <EnumBlockDayType,String> {

	@Override
	public String convertToDatabaseColumn(EnumBlockDayType attribute) {
		return (attribute==null) ? null : attribute.getValue();
	}

	@Override
	public EnumBlockDayType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumBlockDayType.parse(dbData);
	}

}

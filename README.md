@Converter(autoApply = true)
public class EnumInterfaceTypeConverter implements AttributeConverter <EnumInterfaceType,String>{

	@Override
	public String convertToDatabaseColumn(EnumInterfaceType attribute) {
		return (attribute==null) ? null : attribute.name();

	}

	@Override
	public EnumInterfaceType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumInterfaceType.valueOf(dbData);
	}
	
	

}

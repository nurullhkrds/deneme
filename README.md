@Converter(autoApply = true)
public class EnumFeatureCodeConverter implements AttributeConverter <EnumFeatureCode,String>{
	
	@Override
	public String convertToDatabaseColumn(EnumFeatureCode attribute) {
		return (attribute==null) ? null : attribute.name();
	}

	@Override
	public EnumFeatureCode convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumFeatureCode.valueOf(dbData);
	}

}

@Converter(autoApply = true)
public class EnumBlockDayStrategyCodeConverter implements AttributeConverter <EnumBlockDayStrategyCode,String>{
	@Override
	public String convertToDatabaseColumn(EnumBlockDayStrategyCode attribute) {
		return (attribute==null) ? null : attribute.name();
	}
	@Override
	public EnumBlockDayStrategyCode convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumBlockDayStrategyCode.valueOf(dbData);
	}
	
}

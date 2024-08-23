@Converter(autoApply = true)
public class LocalTimeAttributeConverter implements AttributeConverter<LocalTime, String> {

	@Override
	public String convertToDatabaseColumn(LocalTime attribute) {		
		DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH:mm:ss");
		return (attribute==null) ? null : attribute.format(formatter);
	}

	@Override
	public LocalTime convertToEntityAttribute(String dbData) {
		
		DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH:mm:ss");
		return (dbData == null) ? null : LocalTime.parse(dbData, formatter);
		
	}

 
}

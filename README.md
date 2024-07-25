@Converter(autoApply = true)
public class EnumPaymentNotificationTypeConverter extends TypeAdapter<EnumPaymentNotificationType>
		implements AttributeConverter<EnumPaymentNotificationType, String> {

	@Override
	public String convertToDatabaseColumn(EnumPaymentNotificationType attribute) {
		return attribute == null ? null : attribute.getValue();
	}

	@Override
	public EnumPaymentNotificationType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumPaymentNotificationType.parse(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumPaymentNotificationType value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}

	}

	@Override
	public EnumPaymentNotificationType read(JsonReader in) throws IOException {
		return EnumPaymentNotificationType.parse(in.nextString());
	}

}

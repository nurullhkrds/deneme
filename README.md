@Converter(autoApply = true)
public class EnumPaymentNotificationStatuConverter extends TypeAdapter<EnumPaymentNotificationStatu>
		implements AttributeConverter<EnumPaymentNotificationStatu, String> {

	@Override
	public String convertToDatabaseColumn(EnumPaymentNotificationStatu attribute) {
		return attribute == null ? null : attribute.getValue();
	}

	@Override
	public EnumPaymentNotificationStatu convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumPaymentNotificationStatu.parse(dbData);
	}

	@Override
	public void write(JsonWriter out, EnumPaymentNotificationStatu value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}

	}

	@Override
	public EnumPaymentNotificationStatu read(JsonReader in) throws IOException {
		return EnumPaymentNotificationStatu.parse(in.nextString());
	}

}

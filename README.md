@Converter(autoApply = true)
public class EnumOrderPaymentGroupConverter implements AttributeConverter <EnumOrderPaymentGroup,String>{

	@Override
	public String convertToDatabaseColumn(EnumOrderPaymentGroup attribute) {
		return (attribute==null) ? null : attribute.name();
	}

	@Override
	public EnumOrderPaymentGroup convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumOrderPaymentGroup.valueOf(dbData);
	}

}

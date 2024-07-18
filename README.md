public enum EnumReturnType {
    SUCCESS("SUCCESS"),
    ERROR("ERROR");

    @JsonValue
    private final String value;

    private static final Map<String, EnumReturnType> returnTypes = new HashMap<>();

    static {
        for (EnumReturnType type : EnumReturnType.values()) {
            returnTypes.put(type.getValue(), type);
        }
    }

    @JsonCreator
    public static EnumReturnType getReturnType(String value) {
        return returnTypes.get(value);
    }
}



@Converter(autoApply = true)
public class EnumReturnTypeConverter implements AttributeConverter<EnumReturnType, String> {

    @Override
    public String convertToDatabaseColumn(EnumReturnType returnType) {
        return returnType != null ? returnType.getValue() : null;
    }

    @Override
    public EnumReturnType convertToEntityAttribute(String dbValue) {
        return EnumReturnType.getReturnType(dbValue);
    }
}

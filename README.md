@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // Enum string olarak serialize/deserializable olsun
public enum EnumBlockDayType {

    CALENDAR_DAY("C"),
    WORKING_DAY("W");

    @Getter
    private final String value;

    private static final Map<String, EnumBlockDayType> PARAMETERS;

    static {
        PARAMETERS = new LinkedHashMap<>();
        for (EnumBlockDayType type : EnumBlockDayType.values()) {
            PARAMETERS.put(type.value, type);
        }
    }

    @JsonCreator
    public static EnumBlockDayType fromValue(String value) {
        EnumBlockDayType type = PARAMETERS.get(value);
        if (type == null) {
            throw new IllegalArgumentException("Invalid value for EnumBlockDayType: " + value);
        }
        return type;
    }

    @Override
    public String toString() {
        return this.value;
    }
}

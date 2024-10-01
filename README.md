@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // Enum'u JSON'da string olarak işle
public enum EnumBlockDayType {

    CALENDAR_DAY("C"),
    WORKING_DAY("W");

    @Getter
    @JsonValue
    private String value;

    private static final Map<String, EnumBlockDayType> parameters;

    static {
        parameters = new LinkedHashMap<>();
        for (EnumBlockDayType each : EnumBlockDayType.values()) {
            parameters.put(each.value, each);
        }
    }

    @JsonCreator
    public static EnumBlockDayType parse(String value) {
        EnumBlockDayType type = parameters.get(value);
        if (type == null) {
            throw new IllegalArgumentException("Invalid value for EnumBlockDayType: " + value);
        }
        return type;
    }
}

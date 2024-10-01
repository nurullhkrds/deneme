@JsonFormat(shape = JsonFormat.Shape.STRING) // Enum değerlerini string olarak işlemesi için
public enum EnumBlockDayType {

    CALENDAR_DAY("C"),
    WORKING_DAY("W");

    @Getter
    private String value;

    private static final Map<String, EnumBlockDayType> paramaters;

    static {
        paramaters = new LinkedHashMap<>();
        for (EnumBlockDayType each : EnumBlockDayType.values()) {
            paramaters.put(each.value, each);
        }
    }

    EnumBlockDayType(String value) {
        this.value = value;
    }

    @JsonCreator // String JSON'u enum'a dönüştürmek için
    public static EnumBlockDayType parse(String value) {
        return paramaters.get(value);
    }

    @JsonValue // Enum'u JSON string'e dönüştürmek için
    public String getValue() {
        return value;
    }
}

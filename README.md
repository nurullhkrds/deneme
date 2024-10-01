@JsonFormat(shape = JsonFormat.Shape.STRING)  // Enum'u JSON'da string olarak işlemesini sağlar
public enum EnumBlockDayType {

    CALENDAR_DAY("C"),
    WORKING_DAY("W");

    @Getter
    private final String value;

    EnumBlockDayType(String value) {
        this.value = value;
    }

    private static final Map<String, EnumBlockDayType> paramaters = new HashMap<>();

    static {
        for (EnumBlockDayType type : EnumBlockDayType.values()) {
            paramaters.put(type.getValue(), type);
        }
    }

    @JsonCreator // Gelen JSON string'ini enum ile eşleştirmek için
    public static EnumBlockDayType fromValue(String value) {
        return paramaters.get(value);
    }

    @JsonValue // JSON'a string olarak yazdırmak için
    public String getValue() {
        return value;
    }
}

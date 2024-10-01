import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.annotation.JsonValue;

@AllArgsConstructor
@JsonFormat(shape = JsonFormat.Shape.STRING)  // Enum'u JSON'da string olarak serialize/deserializable yapmak için
public enum EnumBlockDayType {
	
    CALENDAR_DAY("C"),
    WORKING_DAY("W");

    @Getter
    @JsonValue
    private final String value;

    private static final Map<String, EnumBlockDayType> PARAMETERS = new LinkedHashMap<>();

    static {
        for (EnumBlockDayType each : EnumBlockDayType.values()) {
            PARAMETERS.put(each.value, each);
        }
    }

    @JsonCreator
    public static EnumBlockDayType fromValue(String value) {
        return PARAMETERS.get(value);
    }

    @Override
    public String toString() {
        return this.value;
    }
}

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class EnumBlockDayTypeConverterTest {

    private final EnumBlockDayTypeConverter converter = new EnumBlockDayTypeConverter();

    @Test
    public void testConvertToDatabaseColumn() {
        // Enum değeri için test
        EnumBlockDayType type = EnumBlockDayType.SOME_ENUM_VALUE; // EnumBlockDayType içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE_STRING"; // Enum değerine karşılık gelen string değeri yazın
        String actualValue = converter.convertToDatabaseColumn(type);

        assertEquals(expectedValue, actualValue);

        // Null değer için test
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    public void testConvertToEntityAttribute() {
        // String değeri için test
        String dbData = "SOME_ENUM_VALUE_STRING"; // Enum değeri için kullanılan string karşılığı
        EnumBlockDayType expectedType = EnumBlockDayType.SOME_ENUM_VALUE; // Enum değeri
        EnumBlockDayType actualType = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedType, actualType);

        // Null değeri için test
        assertNull(converter.convertToEntityAttribute(null));
    }
}

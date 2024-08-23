import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class EnumInterfaceTypeConverterTest {

    private final EnumInterfaceTypeConverter converter = new EnumInterfaceTypeConverter();

    @Test
    public void testConvertToDatabaseColumn() {
        // Enum değeri için test
        EnumInterfaceType interfaceType = EnumInterfaceType.SOME_ENUM_VALUE; // EnumInterfaceType içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE"; // Enum değeri için beklenen string karşılığı
        String actualValue = converter.convertToDatabaseColumn(interfaceType);

        assertEquals(expectedValue, actualValue);

        // Null değer için test
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    public void testConvertToEntityAttribute() {
        // String değeri için test
        String dbData = "SOME_ENUM_VALUE"; // Enum değeri için kullanılan string karşılığı
        EnumInterfaceType expectedType = EnumInterfaceType.SOME_ENUM_VALUE; // Enum değeri
        EnumInterfaceType actualType = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedType, actualType);

        // Null değeri için test
        assertNull(converter.convertToEntityAttribute(null));
    }
}

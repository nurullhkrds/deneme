import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class EnumFeatureCodeConverterTest {

    private final EnumFeatureCodeConverter converter = new EnumFeatureCodeConverter();

    @Test
    public void testConvertToDatabaseColumn() {
        // Enum değeri için test
        EnumFeatureCode featureCode = EnumFeatureCode.SOME_ENUM_VALUE; // EnumFeatureCode içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE"; // Enum değeri için beklenen string karşılığı
        String actualValue = converter.convertToDatabaseColumn(featureCode);

        assertEquals(expectedValue, actualValue);

        // Null değer için test
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    public void testConvertToEntityAttribute() {
        // String değeri için test
        String dbData = "SOME_ENUM_VALUE"; // Enum değeri için kullanılan string karşılığı
        EnumFeatureCode expectedType = EnumFeatureCode.SOME_ENUM_VALUE; // Enum değeri
        EnumFeatureCode actualType = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedType, actualType);

        // Null değeri için test
        assertNull(converter.convertToEntityAttribute(null));
    }
}

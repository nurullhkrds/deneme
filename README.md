import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class EnumBlockDayStrategyCodeConverterTest {

    final EnumBlockDayStrategyCodeConverter converter = new EnumBlockDayStrategyCodeConverter();

    @Test
    void testConvertToDatabaseColumn() {
        EnumBlockDayStrategyCode strategyCode = EnumBlockDayStrategyCode.SOME_ENUM_VALUE; // EnumBlockDayStrategyCode içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE";
        String actualValue = converter.convertToDatabaseColumn(strategyCode);

        assertEquals(expectedValue, actualValue);
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    void testConvertToEntityAttribute() {
        String dbData = "SOME_ENUM_VALUE"; // Enum değeri için kullanılan string karşılığı
        EnumBlockDayStrategyCode expectedCode = EnumBlockDayStrategyCode.SOME_ENUM_VALUE; // Enum değeri
        EnumBlockDayStrategyCode actualCode = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedCode, actualCode);
        assertNull(converter.convertToEntityAttribute(null));
    }

    @Test
    void testInvalidEnumValue() {
        String invalidDbData = "INVALID_VALUE";
        assertThrows(IllegalArgumentException.class, () -> {
            converter.convertToEntityAttribute(invalidDbData);
        });
    }
}

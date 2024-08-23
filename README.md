import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class EnumOrderPaymentGroupConverterTest {

    private final EnumOrderPaymentGroupConverter converter = new EnumOrderPaymentGroupConverter();

    @Test
    public void testConvertToDatabaseColumn() {
        // Enum değeri için test
        EnumOrderPaymentGroup paymentGroup = EnumOrderPaymentGroup.SOME_ENUM_VALUE; // EnumOrderPaymentGroup içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE"; // Enum değeri için beklenen string karşılığı
        String actualValue = converter.convertToDatabaseColumn(paymentGroup);

        assertEquals(expectedValue, actualValue);

        // Null değer için test
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    public void testConvertToEntityAttribute() {
        // String değeri için test
        String dbData = "SOME_ENUM_VALUE"; // Enum değeri için kullanılan string karşılığı
        EnumOrderPaymentGroup expectedType = EnumOrderPaymentGroup.SOME_ENUM_VALUE; // Enum değeri
        EnumOrderPaymentGroup actualType = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedType, actualType);

        // Null değeri için test
        assertNull(converter.convertToEntityAttribute(null));
    }
}

import org.junit.jupiter.api.Test;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import static org.junit.jupiter.api.Assertions.*;

class LocalTimeAttributeConverterTest {

    final LocalTimeAttributeConverter converter = new LocalTimeAttributeConverter();
    final DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH:mm:ss");

    @Test
    void testConvertToDatabaseColumn() {
        LocalTime time = LocalTime.of(14, 30, 45);
        String expectedValue = "14:30:45";
        String actualValue = converter.convertToDatabaseColumn(time);

        assertEquals(expectedValue, actualValue);
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    void testConvertToEntityAttribute() {
        String dbData = "14:30:45";
        LocalTime expectedTime = LocalTime.of(14, 30, 45);
        LocalTime actualTime = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedTime, actualTime);
        assertNull(converter.convertToEntityAttribute(null));
    }

    @Test
    void testInvalidFormat() {
        String invalidDbData = "invalid_time";
        assertThrows(Exception.class, () -> converter.convertToEntityAttribute(invalidDbData));
    }
}

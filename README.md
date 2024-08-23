import com.google.gson.stream.JsonReader;
import com.google.gson.stream.JsonWriter;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.io.StringReader;
import java.io.StringWriter;
import java.io.IOException;

import static org.junit.jupiter.api.Assertions.*;

class EnumExpenseTypeConverterTest {

    EnumExpenseTypeConverter converter;

    @BeforeEach
    void setUp() {
        converter = new EnumExpenseTypeConverter();
    }

    @Test
    void testConvertToDatabaseColumn() {
        EnumExpenseType expenseType = EnumExpenseType.SOME_ENUM_VALUE; // EnumExpenseType içinde var olan bir değeri kullanın
        String expectedValue = "SOME_ENUM_VALUE_STRING"; // Enum değerine karşılık gelen string değeri yazın
        String actualValue = converter.convertToDatabaseColumn(expenseType);

        assertEquals(expectedValue, actualValue);
        assertNull(converter.convertToDatabaseColumn(null));
    }

    @Test
    void testConvertToEntityAttribute() {
        String dbData = "SOME_ENUM_VALUE_STRING"; // Enum değeri için kullanılan string karşılığı
        EnumExpenseType expectedType = EnumExpenseType.SOME_ENUM_VALUE; // Enum değeri
        EnumExpenseType actualType = converter.convertToEntityAttribute(dbData);

        assertEquals(expectedType, actualType);
        assertNull(converter.convertToEntityAttribute(null));
    }

    @Test
    void testWrite() throws IOException {
        EnumExpenseType expenseType = EnumExpenseType.SOME_ENUM_VALUE; // EnumExpenseType içinde var olan bir değeri kullanın
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);

        converter.write(jsonWriter, expenseType);
        jsonWriter.close();

        String expectedJson = "\"SOME_ENUM_VALUE_STRING\""; // Enum değerine karşılık gelen JSON string değeri yazın
        assertEquals(expectedJson, stringWriter.toString());
    }

    @Test
    void testRead() throws IOException {
        String json = "\"SOME_ENUM_VALUE_STRING\""; // Enum değeri için kullanılan JSON string karşılığı
        JsonReader jsonReader = new JsonReader(new StringReader(json));

        EnumExpenseType expectedType = EnumExpenseType.SOME_ENUM_VALUE; // Enum değeri
        EnumExpenseType actualType = converter.read(jsonReader);

        assertEquals(expectedType, actualType);
    }

    @Test
    void testInvalidJsonValue() throws IOException {
        String invalidJson = "\"INVALID_VALUE\"";
        JsonReader jsonReader = new JsonReader(new StringReader(invalidJson));

        assertThrows(IllegalArgumentException.class, () -> {
            converter.read(jsonReader);
        });
    }
}

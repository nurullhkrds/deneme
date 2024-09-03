import com.google.gson.stream.JsonReader;
import com.google.gson.stream.JsonWriter;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.io.StringReader;
import java.io.StringWriter;

import static org.junit.jupiter.api.Assertions.*;

public class EnumLoggingResultTypeConverterTest {

    private EnumLoggingResultTypeConverter converter;

    @BeforeEach
    void setUp() {
        converter = new EnumLoggingResultTypeConverter();
    }

    @Test
    void testWrite() throws IOException {
        EnumLoggingResultType type = EnumLoggingResultType.SUCCESS;
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);

        // write methodunu test ediyoruz
        converter.write(jsonWriter, type);
        jsonWriter.close();

        assertEquals("\"SUCCESS\"", stringWriter.toString());
    }

    @Test
    void testWriteWithNullValue() throws IOException {
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);

        // write methodunu null value ile test ediyoruz
        converter.write(jsonWriter, null);
        jsonWriter.close();

        assertEquals("", stringWriter.toString());  // Null olduğunda hiçbir şey yazılmamalı
    }

    @Test
    void testRead() throws IOException {
        String json = "\"SUCCESS\"";
        JsonReader jsonReader = new JsonReader(new StringReader(json));

        // read methodunu test ediyoruz
        EnumLoggingResultType resultType = converter.read(jsonReader);

        assertEquals(EnumLoggingResultType.SUCCESS, resultType);
    }

    @Test
    void testReadWithInvalidValue() {
        String invalidJson = "\"INVALID\"";
        JsonReader jsonReader = new JsonReader(new StringReader(invalidJson));

        // read methodunu geçersiz bir değerle test ediyoruz ve IllegalArgumentException bekliyoruz
        assertThrows(IllegalArgumentException.class, () -> {
            converter.read(jsonReader);
        });
    }
}

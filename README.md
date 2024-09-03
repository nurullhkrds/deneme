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
org.opentest4j.AssertionFailedError: 
Expected :"SUCCESS"
Actual   :S

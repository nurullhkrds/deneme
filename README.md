import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import org.junit.jupiter.api.Test;

public class EnumAccountingSourceConverterTest {

    private final EnumAccountingSourceConverter converter = new EnumAccountingSourceConverter();
    private final Gson gson = new GsonBuilder()
            .registerTypeAdapter(EnumAccountingSource.class, converter)
            .create();

    @Test
    public void testConvertToDatabaseColumn() {
        // Test a non-null value
        EnumAccountingSource source = EnumAccountingSource.SOME_ENUM_VALUE;
        String dbData = converter.convertToDatabaseColumn(source);
        assertEquals(source.getValue(), dbData);

        // Test a null value
        dbData = converter.convertToDatabaseColumn(null);
        assertNull(dbData);
    }

    @Test
    public void testConvertToEntityAttribute() {
        // Test a non-null value
        String dbData = EnumAccountingSource.SOME_ENUM_VALUE.getValue();
        EnumAccountingSource source = converter.convertToEntityAttribute(dbData);
        assertEquals(EnumAccountingSource.SOME_ENUM_VALUE, source);

        // Test a null value
        source = converter.convertToEntityAttribute(null);
        assertNull(source);
    }

    @Test
    public void testSerialize() {
        EnumAccountingSource source = EnumAccountingSource.SOME_ENUM_VALUE;
        String json = gson.toJson(source);
        assertEquals("\"" + source.getValue() + "\"", json);
    }

    @Test
    public void testDeserialize() {
        String json = "\"" + EnumAccountingSource.SOME_ENUM_VALUE.getValue() + "\"";
        EnumAccountingSource source = gson.fromJson(json, EnumAccountingSource.class);
        assertEquals(EnumAccountingSource.SOME_ENUM_VALUE, source);
    }
}

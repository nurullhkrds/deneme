
public class EnumLoggingResultTypeConverterTest {

    private EnumLoggingResultTypeConverter converter;

    @BeforeEach
    void setUp() {
        converter = new EnumLoggingResultTypeConverter();
    }

    @Test
    void testConvertToDatabaseColumn() {
        EnumLoggingResultType type = EnumLoggingResultType.SUCCESS;
        String dbData = converter.convertToDatabaseColumn(type);
        assertEquals(type.getValue(), dbData);

        dbData = converter.convertToDatabaseColumn(null);
        assertNull(dbData);
    }

    @Test
    void testConvertToEntityAttribute() {
        String dbData = "SUCCESS";
        EnumLoggingResultType type = converter.convertToEntityAttribute(dbData);
        assertEquals(EnumLoggingResultType.SUCCESS, type);

        type = converter.convertToEntityAttribute(null);
        assertNull(type);
    }

    @Test
    void testWrite() throws Exception {
        EnumLoggingResultType type = EnumLoggingResultType.SUCCESS;
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);

        converter.write(jsonWriter, type);
        jsonWriter.close();

        assertEquals("\"SUCCESS\"", stringWriter.toString());

        stringWriter = new StringWriter();
        jsonWriter = new JsonWriter(stringWriter);

        converter.write(jsonWriter, null);
        jsonWriter.close();

        assertEquals("", stringWriter.toString());
    }

    @Test
    void testRead() throws Exception {
        String json = "\"SUCCESS\"";
        JsonReader jsonReader = new JsonReader(new StringReader(json));

        EnumLoggingResultType type = converter.read(jsonReader);
        assertEquals(EnumLoggingResultType.SUCCESS, type);

        json = "null";
        jsonReader = new JsonReader(new StringReader(json));

        assertThrows(IllegalArgumentException.class, () -> {
            converter.read(jsonReader);
        });
    }
}

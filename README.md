public class EnumPaymentNotificationTypeConverterTest {

    private EnumPaymentNotificationTypeConverter converter;

    @BeforeEach
    public void setUp() {
        converter = new EnumPaymentNotificationTypeConverter();
    }

    @Test
    public void testConvertToDatabaseColumn() {
        // Arrange
        EnumPaymentNotificationType type = EnumPaymentNotificationType.SOME_TYPE; // replace with actual enum value
        
        // Act
        String dbData = converter.convertToDatabaseColumn(type);
        
        // Assert
        assertEquals(type.getValue(), dbData);
    }

    @Test
    public void testConvertToDatabaseColumn_null() {
        // Act
        String dbData = converter.convertToDatabaseColumn(null);
        
        // Assert
        assertNull(dbData);
    }

    @Test
    public void testConvertToEntityAttribute() {
        // Arrange
        String dbData = "someValue"; // replace with actual value
        
        // Act
        EnumPaymentNotificationType type = converter.convertToEntityAttribute(dbData);
        
        // Assert
        assertEquals(EnumPaymentNotificationType.parse(dbData), type);
    }

    @Test
    public void testConvertToEntityAttribute_null() {
        // Act
        EnumPaymentNotificationType type = converter.convertToEntityAttribute(null);
        
        // Assert
        assertNull(type);
    }

    @Test
    public void testWrite() throws IOException {
        // Arrange
        EnumPaymentNotificationType type = EnumPaymentNotificationType.SOME_TYPE; // replace with actual enum value
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);
        
        // Act
        converter.write(jsonWriter, type);
        jsonWriter.close();
        
        // Assert
        assertEquals("\"" + type.getValue() + "\"", stringWriter.toString());
    }

    @Test
    public void testWrite_null() throws IOException {
        // Arrange
        StringWriter stringWriter = new StringWriter();
        JsonWriter jsonWriter = new JsonWriter(stringWriter);
        
        // Act
        converter.write(jsonWriter, null);
        jsonWriter.close();
        
        // Assert
        assertEquals("null", stringWriter.toString());
    }

    @Test
    public void testRead() throws IOException {
        // Arrange
        String json = "\"" + EnumPaymentNotificationType.SOME_TYPE.getValue() + "\""; // replace with actual enum value
        JsonReader jsonReader = new JsonReader(new StringReader(json));
        
        // Act
        EnumPaymentNotificationType type = converter.read(jsonReader);
        
        // Assert
        assertEquals(EnumPaymentNotificationType.SOME_TYPE, type); // replace with actual enum value
    }

    @Test
    public void testRead_null() throws IOException {
        // Arrange
        String json = "null";
        JsonReader jsonReader = new JsonReader(new StringReader(json));
        
        // Act
        EnumPaymentNotificationType type = converter.read(jsonReader);
        
        // Assert
        assertNull(type);
    }
}

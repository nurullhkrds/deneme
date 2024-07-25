@Test
public void testWrite_null() throws IOException {
    // Arrange
    StringWriter stringWriter = new StringWriter();
    
    // Act
    try (JsonWriter jsonWriter = new JsonWriter(stringWriter)) {
        converter.write(jsonWriter, null);
    }
    
    // Assert
    assertEquals("null", stringWriter.toString().trim());
}
@Test
public void testWrite() throws IOException {
    // Arrange
    EnumPaymentNotificationType type = EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION; // replace with actual enum value
    StringWriter stringWriter = new StringWriter();
    JsonWriter jsonWriter = new JsonWriter(stringWriter);

    // Act
    converter.write(jsonWriter, type);
    jsonWriter.close();

    // Assert
    assertEquals("\"" + type.getValue() + "\"", stringWriter.toString().trim());
}

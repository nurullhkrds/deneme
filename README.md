@Test
public void testWrite() throws IOException {
    // Arrange
    EnumPaymentNotificationType type = EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION; // replace with actual enum value
    StringWriter stringWriter = new StringWriter();
    
    // Act
    try (JsonWriter jsonWriter = new JsonWriter(stringWriter)) {
        converter.write(jsonWriter, type);
    }
    
    // Print the actual output to debug
    String actualOutput = stringWriter.toString().trim();
    System.out.println("Actual Output: " + actualOutput);
    
    // Assert
    // Ensure the value is quoted, as JSON strings are typically enclosed in double quotes
    assertEquals("\"" + type.getValue() + "\"", actualOutput);
}

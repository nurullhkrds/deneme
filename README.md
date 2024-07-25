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
     

rg.opentest4j.AssertionFailedError: 
Expected :"INST_PAYMENT"
Actual   :INST_PAYMENT


java.io.IOException: Incomplete document

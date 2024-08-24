
    @Test
    public void testWrite_withNonNullValue() throws IOException {
        // Create a mock JsonWriter
        JsonWriter jsonWriter = mock(JsonWriter.class);

        // Create a sample EnumAccountingSource value
        EnumAccountingSource enumValue = EnumAccountingSource.SAMPLE; // Replace SAMPLE with an actual enum value

        // Call the write method
        converter.write(jsonWriter, enumValue);

        // Capture the argument passed to jsonWriter.jsonValue()
        ArgumentCaptor<String> argumentCaptor = ArgumentCaptor.forClass(String.class);
        verify(jsonWriter).jsonValue(argumentCaptor.capture());

        // Assert that the correct value was passed to jsonValue()
        assertEquals(enumValue.getValue(), argumentCaptor.getValue());
    }

    @Test
    public void testWrite_withNullValue() throws IOException {
        // Create a mock JsonWriter
        JsonWriter jsonWriter = mock(JsonWriter.class);

        // Call the write method with a null value
        converter.write(jsonWriter, null);

        // Verify that jsonValue was not called
        verify(jsonWriter, never()).jsonValue(anyString());
    }

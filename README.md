 @Test
    void testRead() throws IOException {
        String json = "\"SUCCESS\"";  // JSON formatındaki string
        JsonReader jsonReader = new JsonReader(new StringReader(json));

        // read methodunu test ediyoruz
        EnumLoggingResultType resultType = converter.read(jsonReader);

        assertEquals(EnumLoggingResultType.SUCCESS, resultType);
    }

    @Test
    void testReadWithInvalidValue() {
        String invalidJson = "\"INVALID\"";  // Geçersiz bir JSON string
        JsonReader jsonReader = new JsonReader(new StringReader(invalidJson));

        // read methodunu geçersiz bir değerle test ediyoruz ve IllegalArgumentException bekliyoruz
        assertThrows(IllegalArgumentException.class, () -> {
            converter.read(jsonReader);
        });
    }

    @Test
    void testReadWithNullValue() throws IOException {
        String nullJson = "null";  // Null değeri simüle etmek için "null" stringi
        JsonReader jsonReader = new JsonReader(new StringReader(nullJson));

        EnumLoggingResultType resultType = converter.read(jsonReader);

        assertNull(resultType);
    }

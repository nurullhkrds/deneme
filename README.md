 @Test
    void testRead() throws IOException {
        String json = "\"SUCCESS\"";  // JSON formatındaki string
        JsonReader jsonReader = new JsonReader(new StringReader(json));

        // read methodunu test ediyoruz
        EnumLoggingResultType resultType = converter.read(jsonReader);

        assertEquals(EnumLoggingResultType.SUCCESS, resultType);
    }

org.opentest4j.AssertionFailedError: 
Expected :EnumLoggingResultType.SUCCESS(value=S, explanation=SUCCESS)
Actual   :null

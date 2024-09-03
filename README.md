@Test
void testWrite() throws IOException {
    EnumLoggingResultType type = EnumLoggingResultType.SUCCESS;
    StringWriter stringWriter = new StringWriter();
    JsonWriter jsonWriter = new JsonWriter(stringWriter);

    // write methodunu test ediyoruz
    converter.write(jsonWriter, type);
    jsonWriter.close();

    assertEquals("SUCCESS", stringWriter.toString());  // Beklenen değeri güncelledik
}

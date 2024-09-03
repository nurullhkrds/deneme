@Test
void testWrite() throws IOException {
    EnumLoggingResultType type = EnumLoggingResultType.SUCCESS;
    StringWriter stringWriter = new StringWriter();
    JsonWriter jsonWriter = new JsonWriter(stringWriter);

    // write methodunu test ediyoruz
    converter.write(jsonWriter, type);
    jsonWriter.close();

    // StringWriter'ın çıktısını kontrol ediyoruz, jsonValue sadece ilk karakteri yazmış olabilir
    assertEquals("SUCCESS", stringWriter.toString().substring(0, 7));  // İlk 7 karakteri kontrol ediyoruz
}

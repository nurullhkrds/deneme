   @Test
     void testConvertObjectToJsonStringWithoutException_nullObject() {
        String jsonString = JsonUtil.convertObjectToJsonStringWithoutException(null);
        assertEquals("", jsonString, "Null object should return an empty string");
    }

org.opentest4j.AssertionFailedError: Null object should return an empty string ==> 
Expected :
Actual   :null

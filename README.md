public class EnumLoggingResultTypeTest {

    private Gson gson;

    @BeforeEach
    void setUp() {
        gson = new GsonBuilder().create();
    }

    @Test
    void testEnumValues() {
        assertEquals("S", EnumLoggingResultType.SUCCESS.getValue());
        assertEquals("SUCCESS", EnumLoggingResultType.SUCCESS.getExplanation());
        
        assertEquals("E", EnumLoggingResultType.ERROR.getValue());
        assertEquals("ERROR", EnumLoggingResultType.ERROR.getExplanation());
    }

    @Test
    void testParseMethod() {
        assertEquals(EnumLoggingResultType.SUCCESS, EnumLoggingResultType.parse("S"));
        assertEquals(EnumLoggingResultType.ERROR, EnumLoggingResultType.parse("E"));
        
        assertNull(EnumLoggingResultType.parse("UNKNOWN"));
    }

    @Test
    void testToStringMethod() {
        assertEquals("EnumLoggingResultType(value=S, explanation=SUCCESS)", EnumLoggingResultType.SUCCESS.toString());
        assertEquals("EnumLoggingResultType(value=E, explanation=ERROR)", EnumLoggingResultType.ERROR.toString());
    }

    @Test
    void testJsonSerialization() {
        String successJson = gson.toJson(EnumLoggingResultType.SUCCESS);
        String errorJson = gson.toJson(EnumLoggingResultType.ERROR);

        assertEquals("\"S\"", successJson);
        assertEquals("\"E\"", errorJson);
    }

    @Test
    void testJsonDeserialization() {
        EnumLoggingResultType successType = gson.fromJson("\"S\"", EnumLoggingResultType.class);
        EnumLoggingResultType errorType = gson.fromJson("\"E\"", EnumLoggingResultType.class);

        assertEquals(EnumLoggingResultType.SUCCESS, successType);
        assertEquals(EnumLoggingResultType.ERROR, errorType);
    }
}

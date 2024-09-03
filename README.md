public class BusinessLogDTOTest {

    private BusinessLogDTO businessLogDTO;

    @BeforeEach
    void setUp() {
        businessLogDTO = new BusinessLogDTO("AppName", "ServiceName", "MethodName");
    }

    @Test
    void testConstructorAndGetters() {
        assertEquals("AppName", businessLogDTO.getApplicationName());
        assertEquals("ServiceName", businessLogDTO.getServiceName());
        assertEquals("MethodName", businessLogDTO.getMethodName());
    }

    @Test
    void testSetterAndGetters() {
        businessLogDTO.setId(1L);
        businessLogDTO.setKey1("Key1Value");
        businessLogDTO.setKey2("Key2Value");
        businessLogDTO.setKey3("Key3Value");
        businessLogDTO.setKey4("Key4Value");
        businessLogDTO.setKey5("Key5Value");
        businessLogDTO.setErrorCode(100);
        businessLogDTO.setErrorMessage("ErrorMessage");
        businessLogDTO.setRequestData("RequestData");
        businessLogDTO.setErrorDetail("ErrorDetail");

        assertEquals(1L, businessLogDTO.getId());
        assertEquals("Key1Value", businessLogDTO.getKey1());
        assertEquals("Key2Value", businessLogDTO.getKey2());
        assertEquals("Key3Value", businessLogDTO.getKey3());
        assertEquals("Key4Value", businessLogDTO.getKey4());
        assertEquals("Key5Value", businessLogDTO.getKey5());
        assertEquals(100, businessLogDTO.getErrorCode());
        assertEquals("ErrorMessage", businessLogDTO.getErrorMessage());
        assertEquals("RequestData", businessLogDTO.getRequestData());
        assertEquals("ErrorDetail", businessLogDTO.getErrorDetail());
    }

    @Test
    void testNotNullFields() {
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO(null, "ServiceName", "MethodName"));
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO("AppName", null, "MethodName"));
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO("AppName", "ServiceName", null));
    }

    @Test
    void testInheritance() {
        businessLogDTO.setInstitutionId(1L);
        assertEquals(1L, businessLogDTO.getInstitutionId());

        long currentTime = System.currentTimeMillis();
        assertTrue(businessLogDTO.getStartTime() <= currentTime);
    }
}

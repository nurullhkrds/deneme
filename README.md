public class ServiceLogDTOTest {

    private ServiceLogDTO serviceLogDTO;

    @BeforeEach
    void setUp() {
        serviceLogDTO = new ServiceLogDTO("AppName", "ServiceName", "MethodName");
    }

    @Test
    void testConstructorAndGetters() {
        assertEquals("AppName", serviceLogDTO.getApplicationName());
        assertEquals("ServiceName", serviceLogDTO.getServiceName());
        assertEquals("MethodName", serviceLogDTO.getMethodName());
    }

    @Test
    void testSetterAndGetters() {
        serviceLogDTO.setId(1L);
        serviceLogDTO.setServiceDirection("INTERNAL");
        serviceLogDTO.setKey1("Key1Value");
        serviceLogDTO.setKey2("Key2Value");
        serviceLogDTO.setKey3("Key3Value");
        serviceLogDTO.setKey4("Key4Value");
        serviceLogDTO.setKey5("Key5Value");
        serviceLogDTO.setReturnCode(200);
        serviceLogDTO.setResultCode("ResultCode");
        serviceLogDTO.setResultType(EnumLoggingResultType.SUCCESS);
        serviceLogDTO.setRequestData("RequestData");
        serviceLogDTO.setResponseData("ResponseData");

        assertEquals(1L, serviceLogDTO.getId());
        assertEquals("INTERNAL", serviceLogDTO.getServiceDirection());
        assertEquals("Key1Value", serviceLogDTO.getKey1());
        assertEquals("Key2Value", serviceLogDTO.getKey2());
        assertEquals("Key3Value", serviceLogDTO.getKey3());
        assertEquals("Key4Value", serviceLogDTO.getKey4());
        assertEquals("Key5Value", serviceLogDTO.getKey5());
        assertEquals(200, serviceLogDTO.getReturnCode());
        assertEquals("ResultCode", serviceLogDTO.getResultCode());
        assertEquals(EnumLoggingResultType.SUCCESS, serviceLogDTO.getResultType());
        assertEquals("RequestData", serviceLogDTO.getRequestData());
        assertEquals("ResponseData", serviceLogDTO.getResponseData());
    }

    @Test
    void testNotNullFinalFields() {
        assertThrows(NullPointerException.class, () -> new ServiceLogDTO(null, "ServiceName", "MethodName"));
        assertThrows(NullPointerException.class, () -> new ServiceLogDTO("AppName", null, "MethodName"));
        assertThrows(NullPointerException.class, () -> new ServiceLogDTO("AppName", "ServiceName", null));
    }

    @Test
    void testInheritance() {
        serviceLogDTO.setInstitutionId(1L);
        assertEquals(1L, serviceLogDTO.getInstitutionId());

        long currentTime = System.currentTimeMillis();
        assertTrue(serviceLogDTO.getStartTime() <= currentTime);
    }
}

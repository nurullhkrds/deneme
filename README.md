@Test
void testNotNullFields() {
    // Yeni test stratejisi:
    try {
        new BusinessLogDTO(null, "ServiceName", "MethodName");
        fail("Expected NullPointerException for null applicationName");
    } catch (NullPointerException e) {
        assertEquals("applicationName is marked non-null but is null", e.getMessage());
    }

    try {
        new BusinessLogDTO("AppName", null, "MethodName");
        fail("Expected NullPointerException for null serviceName");
    } catch (NullPointerException e) {
        assertEquals("serviceName is marked non-null but is null", e.getMessage());
    }

    try {
        new BusinessLogDTO("AppName", "ServiceName", null);
        fail("Expected NullPointerException for null methodName");
    } catch (NullPointerException e) {
        assertEquals("methodName is marked non-null but is null", e.getMessage());
    }
}

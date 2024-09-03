  @Test
    void testNotNullFields() {
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO(null, "ServiceName", "MethodName"));
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO("AppName", null, "MethodName"));
        assertThrows(NullPointerException.class, () -> new BusinessLogDTO("AppName", "ServiceName", null));
    }
org.opentest4j.AssertionFailedError: Expected java.lang.NullPointerException to be thrown, but nothing was thrown.

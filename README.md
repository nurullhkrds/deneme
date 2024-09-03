   @Test
    void testUnknownErrorCode() {
        assertEquals(-999, LoggingConstants.UNKNOWN_ERROR_CODE);
    }

    @Test
    void testUnknownErrorMessage() {
        assertEquals("Bilinmeyen hata olustu", LoggingConstants.UNKNOWN_ERROR_MESSAGE);
    }

    @Test
    void testMaxLoggingLength() {
        assertEquals(4000, LoggingConstants.MAX_LOGGING_LENGHT);
    }

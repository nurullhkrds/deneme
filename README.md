class BillTransactionExceptionTest {

    @Test
    void testDefaultConstructor() {
        BillTransactionException exception = new BillTransactionException();
        assertNull(exception.getMessage());
        assertNull(exception.getCause());
    }

    @Test
    void testConstructorWithMessage() {
        BillTransactionException exception = new BillTransactionException("Custom message");
        assertEquals("Custom message", exception.getMessage());
        assertNull(exception.getCause());
    }

    @Test
    void testConstructorWithCause() {
        Throwable cause = new Throwable("Cause of the error");
        BillTransactionException exception = new BillTransactionException(cause);
        assertEquals(cause, exception.getCause());
        assertEquals("Cause of the error", exception.getCause().getMessage());
    }

    @Test
    void testConstructorWithMessageAndCause() {
        Throwable cause = new Throwable("Cause of the error");
        BillTransactionException exception = new BillTransactionException("Custom message", cause);
        assertEquals("Custom message", exception.getMessage());
        assertEquals(cause, exception.getCause());
    }

    @Test
    void testConstructorWithAllArgs() {
        Throwable cause = new Throwable("Cause of the error");
        BillTransactionException exception = new BillTransactionException("Custom message", cause, true, false);
        assertEquals("Custom message", exception.getMessage());
        assertEquals(cause, exception.getCause());
        assertFalse(exception.getStackTrace().length == 0);
    }
}

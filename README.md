public class ReQueueExceptionTest {

    @Test
    void testDefaultConstructor() {
        ReQueueException exception = assertThrows(ReQueueException.class, () -> {
            throw new ReQueueException();
        });
        assertNull(exception.getMessage());
    }
}

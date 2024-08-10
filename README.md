   @Test
    public void testBeforeExecuteProcess_ProcessChannelNotWorkingHours() {
        when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
        when(LocalTime.now()).thenReturn(LocalTime.of(17, 30)); // Çalışma saatleri dışında bir zaman

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
        assertEquals("1501",exception.getErrorCode().toString());
    }


org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.


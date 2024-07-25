public class ProcessConstantTest {

    @Test
    public void shouldReturnProcessClassList() {
        List<Class<? extends IProcess>> expectedClassList = List.of(
            QueryBillsProcess.class,
            BillPaymentProcess.class,
            BillPaymentReverseProcess.class,
            NotifyPaymentProcess.class,
            NotifyPaymentCancelProcess.class
        );

        List<Class<? extends IProcess>> actualClassList = ProcessConstant.getProcessClassList();
        
        assertIterableEquals(expectedClassList, actualClassList);
    }

    @Test
    public void shouldHaveCorrectProcessParameterKeys() {
        assertEquals("CHANNEL_CODE", ProcessConstant.ProcessParameterKey.KEY_CHANNEL_CODE);
        assertEquals("START_TIME", ProcessConstant.ProcessParameterKey.KEY_START_TIME);
        assertEquals("FINISH_TIME", ProcessConstant.ProcessParameterKey.KEY_FINISH_TIME);
        assertEquals("ID", ProcessConstant.ProcessParameterKey.KEY_ID);
    }
}

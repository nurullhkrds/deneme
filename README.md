org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <com.ykb.payments.bill.common.exception.BillException> but was: <java.lang.NullPointerException>
    @Test
    public void testAfterExecuteProcess_ErrorAndRaiseException() {
        process.error = EnumBillResult.SUCCESS;
        process.shouldRaiseExceptionOnABillError = true;

        assertThrows(BillException.class, () -> {
            process.afterExecuteProcess();
        });
    }

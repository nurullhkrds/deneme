  @Test
     void testBeforeExecuteProcess_Success() throws BillException {
        process.beforeExecuteProcess();
    }

    @Test
     void testAfterExecuteProcess_Success() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.error = EnumBillResult.SUCCESS;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.SUCCESS.getCode().toString(), logDTO.getResultCode());
        assertEquals(EnumBillResult.SUCCESS.getExplanation(), logDTO.getResultText());
        assertEquals(EnumLoggingResultType.SUCCESS.getExplanation(), logDTO.getReturnType());
    }

    @Test
     void testAfterExecuteProcess_ErrorAndRaiseException() {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.logDTO = mock(ProcessLogDTO.class);

        process.error = EnumBillResult.SUCCESS;
        process.shouldRaiseExceptionOnABillError = true;


        assertThrows(BillException.class, () -> {
            process.afterExecuteProcess();
        });
    }


    @Test
    public void testAfterExecuteProcess_ErrorNoRaiseException() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.error = EnumBillResult.SUCCESS;
        process.shouldRaiseExceptionOnABillError = false;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getCode().toString(), logDTO.getResultCode());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getExplanation(), logDTO.getResultText());
        assertEquals(EnumLoggingResultType.ERROR.getExplanation(), logDTO.getReturnType());
    }






java.lang.NullPointerException: Cannot invoke "String.concat(String)" because "responseData1" is null
java.lang.NullPointerException: Cannot invoke "String.concat(String)" because "responseData1" is null
org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <com.ykb.payments.bill.common.exception.BillException> but was: <java.lang.NullPointerException>
com.ykb.payments.bill.common.exception.BillException: Transaction can be made beetween 09:00:00 and 17:00:00 for the given  channel: channelCode

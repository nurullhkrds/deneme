 @Test
    public void testAfterExecuteProcess_ErrorNoRaiseException() throws BillException {
        lenient().when(process.logDTO.getResponseData1()).thenReturn(""); // responseData1'in null olmadığından emin olun

        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.logDTO = mock(ProcessLogDTO.class);
        process.error = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Hata durumunu simüle edin
        process.shouldRaiseExceptionOnABillError = false;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getCode().toString(), process.logDTO.getResultCode());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getExplanation(), process.logDTO.getResultText());
        assertEquals(EnumLoggingResultType.ERROR.getExplanation(), process.logDTO.getReturnType());
    }java.lang.NullPointerException: Cannot invoke "String.concat(String)" because "responseData1" is null

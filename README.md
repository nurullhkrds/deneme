@Test
public void testAfterExecuteProcess_ErrorNoRaiseException() throws BillException {
    process.executionOutput = mock(ProcessExecutionOutput.class);
    process.logDTO = mock(ProcessLogDTO.class);

    // Mock işleminden sonra getResponseData1'in null olmadığından emin olun
    lenient().when(process.logDTO.getResponseData1()).thenReturn(""); // responseData1'in null olmadığından emin olun

    process.error = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Hata durumunu simüle edin
    process.shouldRaiseExceptionOnABillError = false;

    process.afterExecuteProcess();

    assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getCode().toString(), process.logDTO.getResultCode());
    assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getExplanation(), process.logDTO.getResultText());
    assertEquals(EnumLoggingResultType.ERROR.getExplanation(), process.logDTO.getReturnType());
}

@Test
void testAfterExecuteProcess_WithErrorNoRaiseException() throws BillException {
    when(executionOutput.toString()).thenReturn("{\"result\":\"INSTITUTION_CHANNEL_NOT_FOUND\"}");
    process.executionOutput = executionOutput;
    process.error = EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND;  // Örnek bir hata
    process.shouldRaiseExceptionOnABillError = false;

    process.afterExecuteProcess();

    ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
    verify(process.logDTO).setResponseData1(captor.capture());

    String capturedArgument = captor.getValue();
    assertTrue(capturedArgument.contains("{\"result\":\"INSTITUTION_CHANNEL_NOT_FOUND\"}"));
    
    verify(process.logDTO).setResultCode(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode().toString());
    verify(process.logDTO).setResultText(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getExplanation());
    verify(process.logDTO).setReturnType(EnumLoggingResultType.ERROR.getExplanation());
}

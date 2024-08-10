@Test
void testAfterExecuteProcess_Success() throws BillException {
    when(process.logDTO.getResponseData1()).thenReturn("ExistingData");
    when(executionOutput.toString()).thenReturn("executionOutput");

    process.executionOutput = executionOutput;
    process.error = null;  // No error

    process.afterExecuteProcess();

    ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
    verify(process.logDTO).setResponseData1(captor.capture());

    String capturedArgument = captor.getValue();
    System.out.println("Captured Argument: " + capturedArgument); // Argümanı yazdır

    assertTrue(capturedArgument.contains("ExistingData"), "Expected capturedArgument to contain 'ExistingData'");
    assertTrue(capturedArgument.contains("executionOutput"), "Expected capturedArgument to contain 'executionOutput'");

    verify(process.logDTO).setResultCode(EnumBillResult.SUCCESS.getCode().toString());
    verify(process.logDTO).setResultText(EnumBillResult.SUCCESS.getExplanation());
    verify(process.logDTO).setReturnType(EnumLoggingResultType.SUCCESS.getExplanation());
}

import org.mockito.ArgumentCaptor;

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
    assertTrue(capturedArgument.contains("ExistingData"));
    assertTrue(capturedArgument.contains("executionOutput"));

    verify(process.logDTO).setResultCode(EnumBillResult.SUCCESS.getCode().toString());
    verify(process.logDTO).setResultText(EnumBillResult.SUCCESS.getExplanation());
    verify(process.logDTO).setReturnType(EnumLoggingResultType.SUCCESS.getExplanation());
}
@Test
void testAfterExecuteProcess_Success() throws BillException {
    when(process.logDTO.getResponseData1()).thenReturn("ExistingData"); // Önceki veriyi ayarlayın
    when(executionOutput.toString()).thenReturn("executionOutput");

    process.executionOutput = executionOutput;
    process.error = null;  // No error

    process.afterExecuteProcess();

    verify(process.logDTO).setResponseData1(contains("ExistingData\n------OUTPUT-------\nexecutionOutput"));
    verify(process.logDTO).setResultCode(EnumBillResult.SUCCESS.getCode().toString());
    verify(process.logDTO).setResultText(EnumBillResult.SUCCESS.getExplanation());
    verify(process.logDTO).setReturnType(EnumLoggingResultType.SUCCESS.getExplanation());
}

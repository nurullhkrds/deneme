   @Test
     void testAfterExecuteProcess_WithErrorNoRaiseException() throws BillException {
         when(executionOutput.toString()).thenReturn("executionOutput");
         process.executionOutput = executionOutput;
         process.error = EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND;  // An example error
         process.shouldRaiseExceptionOnABillError = false;

         process.afterExecuteProcess();

         verify(process.logDTO).setResponseData1(contains("executionOutput"));
         verify(process.logDTO).setResultCode(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode().toString());
         verify(process.logDTO).setResultText(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getExplanation());
         verify(process.logDTO).setReturnType(EnumLoggingResultType.ERROR.getExplanation());
     }



"processLogDTO.setResponseData1(
    contains("executionOutput")
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testAfterExecuteProcess_WithErrorNoRaiseException(AbstractProcessTest.java:296)
Actual invocations have different arguments:
processLogDTO.getResponseData1(
    
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:204)
processLogDTO.setResponseData1(
    "
------OUTPUT-------
{"result":"INSTITUTION_CHANNEL_NOT_FOUND"}"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:206)
processLogDTO.setResultCode(
    "2022"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:207)
processLogDTO.setResultText(
    "Channel is not defined for the institution"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:208)
processLogDTO.setReturnType(
    "ERROR"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:209)

Comparison Failure: 
<Click to see difference>

Argument(s) are different! Wanted:
processLogDTO.setResponseData1(
    contains("executionOutput")
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testAfterExecuteProcess_WithErrorNoRaiseException(AbstractProcessTest.java:296)
Actual invocations have different arguments:
processLogDTO.getResponseData1(
    
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:204)
processLogDTO.setResponseData1(
    "
------OUTPUT-------
{"result":"INSTITUTION_CHANNEL_NOT_FOUND"}"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:206)
processLogDTO.setResultCode(
    "2022"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:207)
processLogDTO.setResultText(
    "Channel is not defined for the institution"
);
"

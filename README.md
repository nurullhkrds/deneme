     @Test
     void testAfterExecuteProcess_Success() throws BillException {
         when(executionOutput.toString()).thenReturn("executionOutput");
         process.executionOutput = executionOutput;
         process.error = null;  // No error

         process.afterExecuteProcess();

         verify(process.logDTO).setResponseData1(contains("executionOutput"));
         verify(process.logDTO).setResultCode(EnumBillResult.SUCCESS.getCode().toString());
         verify(process.logDTO).setResultText(EnumBillResult.SUCCESS.getExplanation());
         verify(process.logDTO).setReturnType(EnumLoggingResultType.SUCCESS.getExplanation());
     }





-> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testAfterExecuteProcess_Success(AbstractProcessTest.java:258)
Actual invocations have different arguments:
processLogDTO.getResponseData1(
    
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:204)
processLogDTO.setResponseData1(
    "
------OUTPUT-------
{}"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:206)
processLogDTO.setResultCode(
    "0"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:207)
processLogDTO.setResultText(
    "Success"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:208)
processLogDTO.setReturnType(
    "SUCCESS"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:209)

Comparison Failure: 
<Click to see difference>

Argument(s) are different! Wanted:
processLogDTO.setResponseData1(
    contains("executionOutput")
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testAfterExecuteProcess_Success(AbstractProcessTest.java:258)
Actual invocations have different arguments:
processLogDTO.getResponseData1(
    
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:204)
processLogDTO.setResponseData1(
    "
------OUTPUT-------
{}"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:206)
processLogDTO.setResultCode(
    "0"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:207)
processLogDTO.setResultText(
    "Success"
);
-> at com.ykb.payments.bill.transaction.process.common.AbstractProcess.afterExecuteProcess(AbstractProcess.java:208)
processLogDTO.setReturnType(
    "SUCCESS"
);

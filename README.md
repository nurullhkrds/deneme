Wanted but not invoked:
adapterService.queryBills(
    <any>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.process.query.QueryBillsProcessTest.testExecuteProcess_Success(QueryBillsProcessTest.java:170)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
adapterService.queryBills(
    <any>,
    <any string>,
    <any string>
);
  Method isOnlineProcessMethod = QueryBillsProcess.class.getDeclaredMethod("isOnlineProcess");
        isOnlineProcessMethod.setAccessible(true);

        // Replace method to return true
        lenient().doReturn(true).when(queryBillsProcess).isOnlineProcess();

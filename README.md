    @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotActive() {
        when(institutionChannelProcess.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }
org.mockito.exceptions.misusing.UnnecessaryStubbingException: 
Unnecessary stubbings detected.
Clean & maintainable test code requires zero unnecessary code.
Following stubbings are unnecessary (click to navigate to relevant line of code):
  1. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.setUp(AbstractProcessTest.java:60)
  2. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.setUp(AbstractProcessTest.java:61)
  3. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.setUp(AbstractProcessTest.java:62)
  4. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.setUp(AbstractProcessTest.java:64)
  5. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testBeforeExecuteProcess_InstitutionChannelProcessNotActive(AbstractProcessTest.java:135)
Please remove unnecessary stubbings or use 'lenient' strictness. More info: javadoc for UnnecessaryStubbingException class.

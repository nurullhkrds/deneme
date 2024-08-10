    @Test
    public void testBeforeExecuteProcess_InstitutionNotActive() {
        when(institution.getIsActive()).thenReturn(false);
        assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
    }

org.mockito.exceptions.misusing.UnnecessaryStubbingException: 
Unnecessary stubbings detected.
Clean & maintainable test code requires zero unnecessary code.
Following stubbings are unnecessary (click to navigate to relevant line of code):
  1. -> at com.ykb.payments.bill.transaction.process.common.AbstractProcessTest.testBeforeExecuteProcess_InstitutionNotActive(AbstractProcessTest.java:109)
Please remove unnecessary stubbings or use 'lenient' strictness. More info: javadoc for UnnecessaryStubbingException class.

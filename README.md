  @Test
    public void testBeforeExecuteProcess_InstitutionChannelProcessNotActive() {
        when(process.institutionChannelProcess.getIsActive()).thenReturn(false);
        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
        assertEquals(EnumBillResult.INSTITUTION_PROCESS_NOT_ACTIVE.getCode(),exception.getErrorCode());
    }


org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <com.ykb.payments.bill.common.exception.BillException> but was: <java.lang.NullPointerException>

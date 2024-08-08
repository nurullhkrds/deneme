java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.process.common.AbstractProcess$ProcessStepHandler.addFlow(com.ykb.payments.bill.transaction.process.common.ProcessStep)" because "this.stepHandler" is null


    @Test
    void testCheckCustomerQueryLimitFomOperationDisabled() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(false);

        process.executeProcess();

        assertNull(process.getExecutionOutput().getResult());
    }

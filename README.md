  @Test
    public void testToBillPaymentProcessInput() {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        request.setOperatingBranchCode("002");

        BillPaymentProcessInput input = mapper.toBillPaymentProcessInput(request);

        assertEquals("002", input.getBranchCode());
    }java.lang.NullPointerException: Name is null

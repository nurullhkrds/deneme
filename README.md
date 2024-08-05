    @Test
    public void testToBillPaymentProcessInput() {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        request.setOperatingBranchCode("002");

        // MapStruct mapper instance correct initialization
        BillPaymentProcessInput input = mapper.toBillPaymentProcessInput(request);

        // Assertions to validate the mapping
        assertNotNull(input, "The mapped input should not be null");
        assertEquals("002", input.getBranchCode(), "The branchCode should be '002'");
    }

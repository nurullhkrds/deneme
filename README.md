  @Test
    public void testToBillPaymentProcessInput() {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        request.setOperatingBranchCode("002");
        request.setChannelCode("001");
        request.setCurrency("12345");
        request.setBillProvisionId("123");
        request.setInstitutionCode("003");
        request.setPaymentMethodType("method");
        request.setAgentCode("215");
        request.setDebtTypeID("123");
        request.setAccountPaymentMethodDetail(new AccountPaymentMethodDetailWebDTO());
        request.setCashPaymentMethodDetail(new CashPaymentMethodDetailWebDTO());
        request.setCreditCardPaymentMethodDetail(new CreditCardPaymentMethodDetailWebDTO());

        BillPaymentProcessInput input = mapper.toBillPaymentProcessInput(request);

        assertNotNull(input, "The mapped input should not be null");
        assertEquals("002", input.getBranchCode(), "The branchCode should be '002'");
    }
java.lang.IllegalArgumentException: No enum constant com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod.method

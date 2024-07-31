    @Test
    public void testToDoBillPaymentRequest() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        request.setPaymentSourceCode("CARD");
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountCurrencyCode("USD");
        request.setBalanceAccountPaymentInstrument(balanceAccount);

        DoBillPaymentRequest result = mapper.toDoBillPaymentRequest(request);

        assertNotNull(result);
        assertEquals("USD", result.getCurrency());
    }

org.opentest4j.AssertionFailedError: 
Expected :USD
Actual   :YTL

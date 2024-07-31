    @Test
    public void testExtractCurrency() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountCurrencyCode("USD");
        request.setBalanceAccountPaymentInstrument(balanceAccount);
        request.setPaymentSourceCode("ACCOUNT");

        String result = mapper.extractCurrency(request);

        assertEquals("USD", result);
    }
org.opentest4j.AssertionFailedError: 
Expected :USD
Actual   :YTL

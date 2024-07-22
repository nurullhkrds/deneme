 @Test
    public void testCurrencyConverter_NullCurrency() {
        assertEquals(BillTransactionConstant.YTL_CURRENCY, YourClass.currencyConverter(null));
    }

    @Test
    public void testCurrencyConverter_TLCurrency() {
        assertEquals(BillTransactionConstant.YTL_CURRENCY, YourClass.currencyConverter(BillTransactionConstant.TL_CURRENCY));
    }

    @Test
    public void testCurrencyConverter_TryCurrency() {
        assertEquals(BillTransactionConstant.YTL_CURRENCY, YourClass.currencyConverter(BillTransactionConstant.TRY_CURRENCY));
    }

    @Test
    public void testCurrencyConverter_OtherCurrency() {
        String otherCurrency = "USD";
        assertEquals(otherCurrency, YourClass.currencyConverter(otherCurrency));
    }

    // Test for generateCreditCardProvisionRequestId method
    @Test
    public void testGenerateCreditCardProvisionRequestId_DummyMerchant() {
        String channelCode = "123";
        String requestId = YourClass.generateCreditCardProvisionRequestId(channelCode, true);
        assertTrue(requestId.startsWith(BillTransactionConstant.BILLPRE + BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_DUMMY + channelCode));
        assertTrue(requestId.length() > BillTransactionConstant.BILLPRE.length() + BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_DUMMY.length() + channelCode.length());
    }

    @Test
    public void testGenerateCreditCardProvisionRequestId_RealMerchant() {
        String channelCode = "123";
        String requestId = YourClass.generateCreditCardProvisionRequestId(channelCode, false);
        assertTrue(requestId.startsWith(BillTransactionConstant.BILLPRE + BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_REAL + channelCode));
        assertTrue(requestId.length() > BillTransactionConstant.BILLPRE.length() + BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_REAL.length() + channelCode.length());
    }

    // Test for maskCreditCardNo method
    @Test
    public void testMaskCreditCardNo() {
        String cardNo = "1234567890123456";
        String maskChar = "*";
        Integer maskCharLength = 6;
        String expectedMaskedCardNo = "123456******3456";
        assertEquals(expectedMaskedCardNo, YourClass.maskCreditCardNo(cardNo, maskChar, maskCharLength));
    }

    @Test
    public void testMaskCreditCardNo_DifferentMaskChar() {
        String cardNo = "1234567890123456";
        String maskChar = "#";
        Integer maskCharLength = 6;
        String expectedMaskedCardNo = "123456######3456";
        assertEquals(expectedMaskedCardNo, YourClass.maskCreditCardNo(cardNo, maskChar, maskCharLength));
    }

 @Test
    public void testToDoBillPaymentRequest() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        request.setPaymentSourceCode("CARD");
        CreditCardPaymentInstumentDTO creditCard = new CreditCardPaymentInstumentDTO();
        creditCard.setCardNumber("1234");
        creditCard.setCurrencyCode("USD");
        request.setCreditCardPaymentInstrument(creditCard);

        DoBillPaymentRequest result = mapper.toDoBillPaymentRequest(request);

        assertNotNull(result);
        assertEquals("USD", result.getCurrency());
        assertNotNull(result.getCreditCardPaymentMethodDetail());
        assertEquals("1234", result.getCreditCardPaymentMethodDetail().getCardNumber());
    }

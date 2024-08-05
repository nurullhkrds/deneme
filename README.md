   @Test
    void testQueryBills_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        BillException billException = mock(BillException.class);

        when(paymentService.queryBills(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR); // Use a real EnumBillResult value
        when(EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND.getHmnCode()).thenReturn(Collections.emptyList());
        when(EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND.getExplanation()).thenReturn("Explanation");

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals("", result.getResponseMessage().getResponseCode());
        assertEquals("Explanation", result.getResponseMessage().getResponseMessage());
    }
org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.

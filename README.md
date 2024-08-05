    @Test
    void testGetBillPaymentExpense_MicroException() throws MicroException {
        // Arrange
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        MicroException microException = mock(MicroException.class);
        EnumBillResult enumBillResult = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Use a real EnumBillResult value

        when(subscriberService.getBillPaymentExpense(any())).thenThrow(microException);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

java.lang.NullPointerException: Cannot invoke "com.ykb.architecture.micro.error.exception.data.ExceptionData.getErrorCode()" because "exceptionData" is null

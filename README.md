  @Test
    void testGetBillPaymentExpense_MicroException() throws MicroException {
        // Arrange
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        MicroException microException = mock(MicroException.class);
        ExceptionData exceptionData = mock(ExceptionData.class);

        when(subscriberService.getBillPaymentExpense(any())).thenThrow(microException);
        when(microException.getExceptionData()).thenReturn(exceptionData);
        when(exceptionData.getErrorCode()).thenReturn(enumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue());
        when(exceptionData.getErrorMessage()).thenReturn(enumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription());
        when(exceptionData.getErrors()).thenReturn(new HashMap<>()); // or any mock data

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

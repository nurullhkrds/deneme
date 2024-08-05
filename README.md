
    @Test
    void testCancelBillPayment_Success() throws MicroException, BillException {
        // Arrange
        RequestHarmoniCancelBillPayment request = new RequestHarmoniCancelBillPayment();
        CancelBillPaymentResponse cancelBillPaymentResponse = new CancelBillPaymentResponse();
        ResponseHarmoniCancelBillPayment responseHarmoniCancelBillPayment = new ResponseHarmoniCancelBillPayment();
        CancelBillPaymentRequest cancelBillPaymentRequest = new CancelBillPaymentRequest(); // Correct type for the return value

        when(paymentService.cancelBillPayment(any())).thenReturn(cancelBillPaymentResponse);
        when(harmoniMicroMapper.toCancelBillPaymentRequest(any())).thenReturn(cancelBillPaymentRequest);
        when(harmoniMicroMapper.toResponseHarmoniCancelBillPayment(any())).thenReturn(responseHarmoniCancelBillPayment);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> result = harmoniPaymentAdkController.cancelBillPayment(request);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(responseHarmoniCancelBillPayment, result.getResult());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

    @Test
    void testCancelBillPayment_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniCancelBillPayment request = new RequestHarmoniCancelBillPayment();
        BillException billException = mock(BillException.class);
        EnumBillResult enumBillResult = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Use a real EnumBillResult value

        when(paymentService.cancelBillPayment(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(enumBillResult);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> result = harmoniPaymentAdkController.cancelBillPayment(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

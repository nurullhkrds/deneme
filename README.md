   @Test
    void testDoBillPayment_Success() throws MicroException, BillException {
        // Arrange
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        DoBillPaymentResponse doBillPaymentResponse = new DoBillPaymentResponse();
        ResponseHarmoniDoBillPaymentResultDTO responseHarmoniDoBillPaymentResultDTO = new ResponseHarmoniDoBillPaymentResultDTO();

        when(paymentService.doBillPayment(any())).thenReturn(doBillPaymentResponse);
        when(harmoniMicroMapper.toDoBillPaymentRequest(any())).thenReturn(new Object());
        when(harmoniMicroMapper.toResponseHarmoniDoBillPaymentResultDTO(any())).thenReturn(responseHarmoniDoBillPaymentResultDTO);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> result = harmoniPaymentAdkController.doBillPayment(request);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(responseHarmoniDoBillPaymentResultDTO, result.getResult());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

    @Test
    void testDoBillPayment_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        BillException billException = mock(BillException.class);
        EnumBillResult enumBillResult = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Use a real EnumBillResult value

        when(paymentService.doBillPayment(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(enumBillResult);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> result = harmoniPaymentAdkController.doBillPayment(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

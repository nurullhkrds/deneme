 @Test
    void testDoBillPayment_Success() throws MicroException, BillException {
        // Arrange
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        DoBillPaymentResponse doBillPaymentResponse = new DoBillPaymentResponse();
        ResponseHarmoniDoBillPaymentResultDTO responseHarmoniDoBillPaymentResultDTO = new ResponseHarmoniDoBillPaymentResultDTO();
        DoBillPaymentRequest doBillPaymentRequest = new DoBillPaymentRequest(); // Correct type for the return value

        when(paymentService.doBillPayment(any())).thenReturn(doBillPaymentResponse);
        when(harmoniMicroMapper.toDoBillPaymentRequest(any())).thenReturn(doBillPaymentRequest);
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

  @Test
    void testGetBillPaymentExpense_Success() throws MicroException {
        // Arrange
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        GetBillPaymentExpenseResponseDTO getBillPaymentExpenseResponseDTO = new GetBillPaymentExpenseResponseDTO();
        ResponseHarmoniGetBillPaymentExpense responseHarmoniGetBillPaymentExpense = new ResponseHarmoniGetBillPaymentExpense();
        GetBillPaymentExpenseRequestDTO getBillPaymentExpenseRequestDTO = new GetBillPaymentExpenseRequestDTO(); // Correct type for the return value

        when(subscriberService.getBillPaymentExpense(any())).thenReturn(getBillPaymentExpenseResponseDTO);
        when(harmoniMicroMapper.toGetBillPaymentExpenseRequestDTO(any())).thenReturn(getBillPaymentExpenseRequestDTO);
        when(harmoniMicroMapper.toResponseHarmoniGetBillPaymentExpense(any())).thenReturn(responseHarmoniGetBillPaymentExpense);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(responseHarmoniGetBillPaymentExpense, result.getResult());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

    @Test
    void testGetBillPaymentExpense_MicroException() throws MicroException {
        // Arrange
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        MicroException microException = mock(MicroException.class);
        EnumBillResult enumBillResult = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Use a real EnumBillResult value

        when(subscriberService.getBillPaymentExpense(any())).thenThrow(microException);
        when(microException.getBillResult()).thenReturn(enumBillResult);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

@Test
    void testGetReconCount_Success() throws MicroException {
        // Arrange
        boolean isPayment = true;
        Date reconciliationDate = new Date(1622494800000L); // Replace with appropriate long value
        String productCode = "productCode";
        String institutionCode = "institutionCode";
        CountDTO countDTO = new CountDTO();

        when(paymentService.getReconCount(isPayment, reconciliationDate, productCode, institutionCode)).thenReturn(countDTO);

        // Act
        HarmoniCoreServiceResultDTO<CountDTO> result = harmoniPaymentAdkController.getReconCount(isPayment, reconciliationDate, productCode, institutionCode);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(countDTO, result.getResult());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

    @Test
    void testGetReconCount_MicroException() throws MicroException {
        // Arrange
        boolean isPayment = true;
        Date reconciliationDate = new Date(1622494800000L); // Replace with appropriate long value
        String productCode = "productCode";
        String institutionCode = "institutionCode";
        MicroException microException = mock(MicroException.class);
        ExceptionData exceptionData = mock(ExceptionData.class);

        when(paymentService.getReconCount(isPayment, reconciliationDate, productCode, institutionCode)).thenThrow(microException);
        when(microException.getExceptionData()).thenReturn(exceptionData);
        when(exceptionData.getErrorCode()).thenReturn(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue());
        when(exceptionData.getErrorMessage()).thenReturn(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription());

        // Act
        HarmoniCoreServiceResultDTO<CountDTO> result = harmoniPaymentAdkController.getReconCount(isPayment, reconciliationDate, productCode, institutionCode);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

 @Test
    void testQueryBills_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        BillException billException = mock(BillException.class);

        when(paymentService.queryBills(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(EnumBillResult.SOME_ERROR_CODE); // Use a real EnumBillResult value
        when(EnumBillResult.SOME_ERROR_CODE.getHmnCode()).thenReturn(Collections.emptyList());
        when(EnumBillResult.SOME_ERROR_CODE.getExplanation()).thenReturn("Explanation");

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals("", result.getResponseMessage().getResponseCode());
        assertEquals("Explanation", result.getResponseMessage().getResponseMessage());
    }

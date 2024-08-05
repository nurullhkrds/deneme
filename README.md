   @Test
    void testQueryBills_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        BillException billException = mock(BillException.class);

        EnumBillResult enumBillResult = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Use a real EnumBillResult value

        when(paymentService.queryBills(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(enumBillResult);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(enumBillResult.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(enumBillResult.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

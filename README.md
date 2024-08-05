
    @Test
    void testQueryBills_BillException() throws MicroException, BillException {
        // Arrange
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        BillException billException = mock(BillException.class);
        EnumBillResult billResult = mock(EnumBillResult.class);

        when(paymentService.queryBills(any())).thenThrow(billException);
        when(billException.getBillResult()).thenReturn(billResult);
        when(billResult.getHmnCode()).thenReturn(Collections.emptyList());
        when(billResult.getExplanation()).thenReturn("Explanation");

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals("", result.getResponseMessage().getResponseCode());
        assertEquals("Explanation", result.getResponseMessage().getResponseMessage());
    }

org.mockito.exceptions.base.MockitoException: 
Cannot mock/spy class com.ykb.payments.bill.common.enums.EnumBillResult
Mockito cannot mock/spy because :
 - final class

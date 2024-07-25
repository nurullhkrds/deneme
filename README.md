 @Test
    void getCustomerPaidBillList_shouldReturnCombinedBillList_whenBothListsAreNotEmpty() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();
        PaidBillResponseWebDTO microBill = new PaidBillResponseWebDTO();
        PaidBillResponseWebDTO harmoniBill = new PaidBillResponseWebDTO();
        microBillList.add(microBill);
        harmoniBillList.add(harmoniBill);
        GetCustomerPaidBillListResponse expectedResponse = new GetCustomerPaidBillListResponse();
        expectedResponse.setBillList(List.of(microBill, harmoniBill));

        // Mock the behavior of getBillList and getHarmoniBillList
        when(paymentService.getBillList(request)).thenReturn(microBillList);
        when(paymentService.getHarmoniBillList(request)).thenReturn(harmoniBillList);

        // Mock the behavior of BillValidationUtil
        doNothing().when(billValidationUtil).validateCondition(anyBoolean(), any(), any());

        // When
        GetCustomerPaidBillListResponse actualResponse = paymentService.getCustomerPaidBillList(request);

        // Then
        assertEquals(expectedResponse, actualResponse);
        verify(billValidationUtil).validateCondition(true, EnumBillResult.PAID_BILL_NOT_FOUND_ERROR, BillTransactionConstant.APP_NAME);
    }

    @Test
    void getCustomerPaidBillList_shouldThrowException_whenCombinedListIsEmpty() {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> emptyList = Collections.emptyList();

        // Mock the behavior of getBillList and getHarmoniBillList
        when(paymentService.getBillList(request)).thenReturn(emptyList);
        when(paymentService.getHarmoniBillList(request)).thenReturn(emptyList);

        // Expectation
        assertThrows(MicroException.class, () -> paymentService.getCustomerPaidBillList(request));

        // Verify that validation was performed
        verify(billValidationUtil).validateCondition(false, EnumBillResult.PAID_BILL_NOT_FOUND_ERROR, BillTransactionConstant.APP_NAME);
    }

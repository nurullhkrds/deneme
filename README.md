@Test
void testCheckCustomerQueryLimitFomOperationDisabled() {
    when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(false);

    process.executeProcess();

    // Check that the method returns early and does not set any error
    assertNull(process.getExecutionOutput().getResult());
}

@Test
void testCheckCustomerQueryLimitPaymentNotAllowed() {
    when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

    PaymentAllowedResponse mockResponse = new PaymentAllowedResponse();
    mockResponse.setPaymentAllowed(false);
    when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), any(), any(), anyString())).thenReturn(mockResponse);

    process.executeProcess();

    // Check that the method sets the error to BILL_QUERY_LIMIT_REACHED
    assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, process.getExecutionOutput().getResult());
}

@Test
void testQueryFromServiceSuccess() throws BillException {
    QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
    mockQueriedBillDTO.setBillDueDate(LocalDate.now());
    mockQueriedBillDTO.setBillNo("12345");

    QueryBillsAdapterResponse mockResponse = new QueryBillsAdapterResponse();
    mockResponse.setInternalResultCode(String.valueOf(EnumBillResult.SUCCESS.getCode()));
    mockResponse.setBills(List.of(mockQueriedBillDTO));

    when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);

    process.executeProcess();

    // Check that the queriedBillDTOList is set correctly
    assertEquals(1, process.getExecutionOutput().getProvisionDTOList().size());
}

@Test
void testEliminateBillsNoBillsFound() {
    when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString())).thenReturn(new ResponseGetCustomerPaidBillList());

    process.executeProcess();

    // Check that the method sets the error to BILL_NOT_FOUND
    assertEquals(EnumBillResult.BILL_NOT_FOUND, process.getExecutionOutput().getResult());
}

@Test
void testInvalidateNotPaidProvisions() {
    process.executeProcess();

    // Verify that invalidateNotPaidProvisions method is called
    verify(provisionService, times(1)).invalidateNotPaidProvisions(anyLong(), anyString());
}

@Test
void testCreateProvisions() {
    QueriedBillDTO mockQueriedBillDTO = new QueriedBillDTO();
    mockQueriedBillDTO.setBillDueDate(LocalDate.now());
    mockQueriedBillDTO.setBillNo("12345");

    process.setQueriedBillDTOList(List.of(mockQueriedBillDTO));

    process.executeProcess();

    // Verify that createProvisions method is called
    verify(provisionService, times(1)).createProvisions(anyList());
}

@Test
void testUpdateCustomerQueryLimitFomOperationDisabled() {
    when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(false);

    process.executeProcess();

    // Verify that publishInquiryLimitationNotification method is not called
    verify(paymentEventPublisher, times(0)).publishInquiryLimiationNotification(any(NotifyInquiryLimitationRequest.class));
}

@Test
void testUpdateCustomerQueryLimit() {
    when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

    process.executeProcess();

    // Verify that publishInquiryLimitationNotification method is called
    verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any(NotifyInquiryLimitationRequest.class));
}

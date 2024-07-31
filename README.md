@Test
void testOnPaymentCancelCreatedNotificationEvent() {
    // BillPaymentCancelEvent mock
    BillPaymentCancelEvent event = mock(BillPaymentCancelEvent.class);

    // PaymentCancelDTO mock
    PaymentCancelDTO cancelDTO = mock(PaymentCancelDTO.class);
    when(event.getCancelRecord()).thenReturn(cancelDTO);
    when(cancelDTO.getCreatedBy()).thenReturn("user");
    when(cancelDTO.getBranchCode()).thenReturn("branchCode");
    when(cancelDTO.getChannelCode()).thenReturn("channelCode");
    when(cancelDTO.getChannelSessionId()).thenReturn("channelSessionId");
    when(cancelDTO.getChannelTransactionId()).thenReturn("channelTransactionId");

    // Institution mock
    InstitutionDTO institutionDTO = mock(InstitutionDTO.class);
    when(event.getInstitution()).thenReturn(institutionDTO);
    when(institutionDTO.getId()).thenReturn(123456L);
    when(institutionDTO.getInstitutionCode()).thenReturn("institutionCode");

    // Product mock
    ProductDTO product = mock(ProductDTO.class);
    when(institutionDTO.getProduct()).thenReturn(product);
    when(product.getCode()).thenReturn("productCode");

    when(event.getPaymentNotificationId()).thenReturn(123456L);

    // Call the method under test
    paymentEventListener.onPaymentCancelCreatedNotificationEvent(event);

    // Verify that sendPaymentCancelNotificationEvent was called with the correct parameters
    ArgumentCaptor<PaymentCancelNotificationEvent> captor = ArgumentCaptor.forClass(PaymentCancelNotificationEvent.class);
    verify(paymentNotificationEventProducer, times(1)).sendPaymentCancelNotificationEvent(captor.capture());

    // Assertions to check if the captured event has the expected values
    PaymentCancelNotificationEvent capturedEvent = captor.getValue();
    assertEquals("user", capturedEvent.getCreatedBy());
    assertEquals("branchCode", capturedEvent.getBranchCode());
    assertEquals("channelCode", capturedEvent.getChannelCode());
    assertEquals("channelSessionId", capturedEvent.getChannelSessionId());
    assertEquals("channelTransactionId", capturedEvent.getChannelTransactionId());
    assertEquals(Long.valueOf(123456L), capturedEvent.getInstitutionId());  // Beklenen değer Long olmalı
    assertEquals(Long.valueOf(123456L), capturedEvent.getPaymentNotificationId());  // Beklenen değer Long olmalı
    assertEquals("productCode", capturedEvent.getProductCode());
    assertEquals("institutionCode", capturedEvent.getInstitutionCode());
}

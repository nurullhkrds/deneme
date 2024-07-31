@Test
void testOnPaymentCreatedNotificationEvent() {
    // BillPaymentEvent mock
    BillPaymentEvent billPaymentEvent = mock(BillPaymentEvent.class);

    // PaymentDTO mock
    PaymentDTO paymentDTO = mock(PaymentDTO.class);
    when(billPaymentEvent.getPaymentDTO()).thenReturn(paymentDTO);
    when(paymentDTO.getCreatedBy()).thenReturn("user");
    when(paymentDTO.getBranchCode()).thenReturn("branchCode");
    when(paymentDTO.getChannelCode()).thenReturn("channelCode");
    when(paymentDTO.getChannelSessionId()).thenReturn("channelSessionId");
    when(paymentDTO.getChannelTransactionId()).thenReturn("channelTransactionId");
    when(paymentDTO.getInstitutionId()).thenReturn(123456L);

    // InstitutionDTO mock
    InstitutionDTO institutionDTO = mock(InstitutionDTO.class);
    when(billPaymentEvent.getInstitutionDTO()).thenReturn(institutionDTO);
    when(institutionDTO.getInstitutionCode()).thenReturn("institutionCode");

    // Product mock
    ProductDTO product = mock(ProductDTO.class);
    when(institutionDTO.getProduct()).thenReturn(product);
    when(product.getCode()).thenReturn("productCode");

    when(billPaymentEvent.getPaymentNotificationId()).thenReturn(123456L);

    // Call the method under test
    paymentEventListener.onPaymentCreatedNotificationEvent(billPaymentEvent);

    // Verify that sendPaymentNotificationEvent was called with the correct parameters
    ArgumentCaptor<PaymentNotificationEvent> captor = ArgumentCaptor.forClass(PaymentNotificationEvent.class);
    verify(paymentNotificationEventProducer, times(1)).sendPaymentNotificationEvent(captor.capture());

    // Assertions to check if the captured event has the expected values
    PaymentNotificationEvent capturedEvent = captor.getValue();
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

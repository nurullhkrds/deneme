@Test
void testSendAckForCreditCardProvision_MicroException() throws BusinessException, ServiceCallException {
    PaymentDTO paymentDTO = new PaymentDTO();
    paymentDTO.setProvisionRequestId("1");
    paymentDTO.setChannelTransactionId("1");
    paymentDTO.setChannelSessionId("1");
    when(paymentService.getPayment(anyLong())).thenReturn(paymentDTO);

    PaymentNotification paymentNotification = new PaymentNotification();
    paymentNotification.setRetryCount(1);
    when(repository.findById(anyLong())).thenReturn(Optional.of(paymentNotification));
    
    MicroException microException = new MicroException(new ExceptionData("500", "Microservice error"));
    when(cardProvisionService.doProvisionAcknowledge(any(CardProvisionAcknowledgeRequest.class), anyString(), anyString()))
            .thenThrow(microException);
    
    mockFieldValue(paymentNotificationService, "creditCardProvisionACKNotificationMaxTryCount", 3);

    Assertions.assertThrows(ReQueueException.class, () -> {
        paymentNotificationService.sendAckForCreditCardProvision(1L, 1L, false);
    });

    assertEquals(EnumPaymentNotificationStatu.ERROR, paymentNotification.getNotificationStatus());
    assertEquals("500", paymentNotification.getResponseCode());
    assertEquals("Microservice error", paymentNotification.getResponseMessage());
    verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
}

@Test
void testSendAckForCreditCardProvision_GenericException() throws BusinessException, ServiceCallException {
    PaymentDTO paymentDTO = new PaymentDTO();
    paymentDTO.setProvisionRequestId("1");
    paymentDTO.setChannelTransactionId("1");
    paymentDTO.setChannelSessionId("1");
    when(paymentService.getPayment(anyLong())).thenReturn(paymentDTO);

    PaymentNotification paymentNotification = new PaymentNotification();
    paymentNotification.setRetryCount(1);
    when(repository.findById(anyLong())).thenReturn(Optional.of(paymentNotification));

    when(cardProvisionService.doProvisionAcknowledge(any(CardProvisionAcknowledgeRequest.class), anyString(), anyString()))
            .thenThrow(new RuntimeException("Unexpected error"));

    mockFieldValue(paymentNotificationService, "creditCardProvisionACKNotificationMaxTryCount", 3);

    Assertions.assertThrows(ReQueueException.class, () -> {
        paymentNotificationService.sendAckForCreditCardProvision(1L, 1L, false);
    });

    assertEquals(EnumPaymentNotificationStatu.ERROR, paymentNotification.getNotificationStatus());
    assertEquals("-999", paymentNotification.getResponseCode());
    assertEquals("Unexpected error", paymentNotification.getResponseMessage());
    verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
}

@Test
void testCreditCardReverseProvision_MicroException() throws BusinessException, ServiceCallException {
    PaymentDTO paymentDTO = new PaymentDTO();
    paymentDTO.setProvisionRequestId("1");
    paymentDTO.setChannelTransactionId("1");
    paymentDTO.setChannelSessionId("1");
    when(paymentService.getPayment(anyLong())).thenReturn(paymentDTO);
    when(paymentService.getPaymentCancel(anyLong())).thenReturn(new PaymentCancelDTO());

    PaymentNotification paymentNotification = new PaymentNotification();
    paymentNotification.setRetryCount(1);
    when(repository.findById(anyLong())).thenReturn(Optional.of(paymentNotification));

    MicroException microException = new MicroException(new ExceptionData("500", "Microservice error"));
    when(cardProvisionService.doReverseProvision(any(CardReverseProvisionRequest.class)))
            .thenThrow(microException);
    
    mockFieldValue(paymentNotificationService, "creditCardReverseProvisionNotificationMaxTryCount", 3);

    Assertions.assertDoesNotThrow(() -> {
        paymentNotificationService.creditCardReverseProvision(1L, 1L, 1L, true);
    });

    assertEquals(EnumPaymentNotificationStatu.ERROR, paymentNotification.getNotificationStatus());
    assertEquals("500", paymentNotification.getResponseCode());
    assertEquals("Microservice error", paymentNotification.getResponseMessage());
    verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
}

  @Test
    void testOnPaymentCreatedNotificationEvent() {
        BillPaymentEvent billPaymentEvent = mock(BillPaymentEvent.class);
        // Mock billPaymentEvent and its methods
        // For example:
        PaymentDTO paymentDTO = mock(PaymentDTO.class);
        when(billPaymentEvent.getPaymentDTO()).thenReturn(paymentDTO);
        when(paymentDTO.getCreatedBy()).thenReturn("user");

        paymentEventListener.onPaymentCreatedNotificationEvent(billPaymentEvent);

        verify(paymentNotificationEventProducer, times(1))
                .sendPaymentNotificationEvent(any(PaymentNotificationEvent.class));
    }

java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO.getProduct()" because the return value of "com.ykb.payments.bill.transaction.payment.model.BillPaymentEvent.getInstitutionDTO()" is null

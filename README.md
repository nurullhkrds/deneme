    @Test
    void testOnPaymentCancelCreatedNotificationEvent() {
        BillPaymentCancelEvent event = mock(BillPaymentCancelEvent.class);
        // Mock event and its methods

        paymentEventListener.onPaymentCancelCreatedNotificationEvent(event);

        verify(paymentNotificationEventProducer, times(1))
                .sendPaymentCancelNotificationEvent(any(PaymentCancelNotificationEvent.class));
    }


java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.payment.dto.PaymentCancelDTO.getCreatedBy()" because the return value of "com.ykb.payments.bill.transaction.payment.model.BillPaymentCancelEvent.getCancelRecord()" is null

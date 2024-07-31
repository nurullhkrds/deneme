  @Test
    public void testFindPublishPaymentEvent() {
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));


        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.CARD);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }


Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.


	at com.ykb.payments.bill.transaction.payment.event.PaymentEventPublisherTest.testFindPublishPaymentEv

@Test
    public void testFindPublishPaymentEvent() {
        // Test for INSTITUTION_PAYMENT_NOTIFICATION
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));

        // Test for CRD_PRVSN_ACK
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        when(paymentDTO.getPaymentMethod())
                .thenReturn(EnumPaymentMethod.CARD);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }


org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.

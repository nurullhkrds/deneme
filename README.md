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

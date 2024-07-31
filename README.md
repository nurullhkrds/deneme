 @Test
    public void testFindPublishPaymentEventWithInstitutionPaymentNotification() {
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));
    }

    @Test
    public void testFindPublishPaymentEventWithCardProvisionACK() {
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        when(paymentDTO.getPaymentMethod()).thenReturn(EnumPaymentMethod.CARD);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }

    @Test
    public void testFindPublishPaymentEventWithNoNotification() {
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(Collections.emptyList());

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, never()).publishEvent(any());
    }

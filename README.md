
    @Test
    void testFindPublishPaymentEventV2() {
        // Create mock data for testing
        PaymentDTO paymentDTO = new PaymentDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        
        PaymentNotificationDTO notification1 = new PaymentNotificationDTO();
        notification1.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        notification1.setId(1L);
        
        PaymentNotificationDTO notification2 = new PaymentNotificationDTO();
        notification2.setNotificationType(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        notification2.setId(2L);
        
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setInstitutionDTO(institutionDTO);
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Arrays.asList(notification1, notification2));
        
        // Call the method under test
        paymentEventPublisher.findPublishPaymentEventV2(publishPaymentTypeDTO);
        
        // Capture and verify the published events
        ArgumentCaptor<Object> eventCaptor = ArgumentCaptor.forClass(Object.class);
        verify(eventPublisher, times(2)).publishEvent(eventCaptor.capture());
        
        List<Object> publishedEvents = eventCaptor.getAllValues();
        
        assertEquals(2, publishedEvents.size());
        
        BillPaymentEvent billPaymentEvent = (BillPaymentEvent) publishedEvents.get(0);
        assertNotNull(billPaymentEvent);
        assertEquals(paymentDTO, billPaymentEvent.getPaymentDTO());
        assertEquals(institutionDTO, billPaymentEvent.getInstitutionDTO());
        assertEquals(1L, billPaymentEvent.getPaymentNotificationId());
        
        CreditCardProvisionACKEventDTO creditCardProvisionACKEvent = (CreditCardProvisionACKEventDTO) publishedEvents.get(1);
        assertNotNull(creditCardProvisionACKEvent);
        assertEquals(2L, creditCardProvisionACKEvent.getPaymentNotificationId());
        assertEquals(paymentDTO.getId(), creditCardProvisionACKEvent.getPaymentId());
    }

    // Helper method to create a PublishPaymentTypeDTO for testing
    private PublishPaymentTypeDTO createPublishPaymentTypeDTO(EnumPaymentNotificationType notificationType, EnumProvisionType provisionType) {
        PaymentNotificationDTO notificationDTO = new PaymentNotificationDTO();
        notificationDTO.setNotificationType(notificationType);
        
        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setPaymentMethod(new PaymentMethodDTO(provisionType));

        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Collections.singletonList(notificationDTO));
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setInstitutionDTO(new InstitutionDTO());

        return publishPaymentTypeDTO;
    }

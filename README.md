   public void publishCreditCardProvisionReverseEvent(PaymentDTO paymentDTO,PaymentCancelDTO paymentCancelDTO, Long paymentNotificationID) {
    	CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO = new CreditCardProvisionReverseEventDTO(); 
    	creditCardProvisionReverseEventDTO.setPaymentId(paymentDTO.getId());
    	creditCardProvisionReverseEventDTO.setPaymentCancelId(paymentCancelDTO.getId());
    	creditCardProvisionReverseEventDTO.setPaymentNotificationId(paymentNotificationID);
        eventPublisher.publishEvent(creditCardProvisionReverseEventDTO);
        
    }
    
    public void publishPaymentLimiationNotification(NotifyPaymentLimitationRequest notifyPaymentLimitationRequest) {    	
        eventPublisher.publishEvent(notifyPaymentLimitationRequest);        
    }    
    
    public void publishInquiryLimiationNotification(NotifyInquiryLimitationRequest notifyInquiryLimitationRequest) {    	
        eventPublisher.publishEvent(notifyInquiryLimitationRequest);
        
    }
	public void findPublishPaymentEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Map<EnumPaymentNotificationType, BiConsumer<PublishPaymentTypeDTO, PaymentNotificationDTO>> consumerMap = new HashMap<>();
		consumerMap.put(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION, this::publishPaymentNotificationEventV2);
		consumerMap.put(EnumPaymentNotificationType.CRD_PRVSN_ACK, this::publishCreditCardProvisionACKEventV2);

		publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList()
				.forEach(each -> consumerMap.get(each.getNotificationType()).accept(publishPaymentTypeDTO, each));

	}
	
    public void publishPaymentNotificationEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO, PaymentNotificationDTO notification) {
    	BillPaymentEvent billPaymentEvent = new BillPaymentEvent();
		billPaymentEvent.setPaymentDTO(publishPaymentTypeDTO.getPaymentDTO());
		billPaymentEvent.setInstitutionDTO(publishPaymentTypeDTO.getInstitutionDTO());
		billPaymentEvent.setPaymentNotificationId(notification.getId());
		eventPublisher.publishEvent(billPaymentEvent);
    }
    
    public void publishCreditCardProvisionACKEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO, PaymentNotificationDTO notification) {
        CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO = new CreditCardProvisionACKEventDTO();
        creditCardProvisionACKEventDTO.setPaymentId(publishPaymentTypeDTO.getPaymentDTO().getId());
        creditCardProvisionACKEventDTO.setPaymentNotificationId(notification.getId());
        eventPublisher.publishEvent(creditCardProvisionACKEventDTO);
    }

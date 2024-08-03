	public void findPublishPaymentEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Map<EnumPaymentNotificationType, BiConsumer<PublishPaymentTypeDTO, PaymentNotificationDTO>> consumerMap = new HashMap<>();
		consumerMap.put(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION, this::publishPaymentNotificationEventV2);
		consumerMap.put(EnumPaymentNotificationType.CRD_PRVSN_ACK, this::publishCreditCardProvisionACKEventV2);

		publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList()
				.forEach(each -> consumerMap.get(each.getNotificationType()).accept(publishPaymentTypeDTO, each));

	}

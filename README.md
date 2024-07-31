@Component
public class PaymentEventPublisher {

    private ApplicationEventPublisher eventPublisher;
        
    @Autowired
    public PaymentEventPublisher(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }
    
	public void findPublishPaymentEvent(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Optional<PaymentNotificationDTO> findPaymentNotificationEvent = publishPaymentTypeDTO
				.getInsertedPaymentNotificationDTOList().stream()
				.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
						.equals(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION.getValue()))
				.findFirst();

		if (findPaymentNotificationEvent.isPresent()) {
			publishPaymentNotificationEvent(publishPaymentTypeDTO.getPaymentDTO(),
					publishPaymentTypeDTO.getInstitutionDTO(), findPaymentNotificationEvent.get().getId());

		}

		if (EnumProvisionType.CARD
				.equals(publishPaymentTypeDTO.getPaymentDTO().getPaymentMethod().getProvisionType())) {
			Optional<PaymentNotificationDTO> findProvisionACKEvent = publishPaymentTypeDTO
					.getInsertedPaymentNotificationDTOList().stream()
					.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
							.equals(EnumPaymentNotificationType.CRD_PRVSN_ACK.getValue()))
					.findFirst();

			if (findProvisionACKEvent.isPresent()) {
				publishCreditCardProvisionACKEvent(publishPaymentTypeDTO.getPaymentDTO().getId(),
						findProvisionACKEvent.get().getId());
			}

		}
	}

@RequiredArgsConstructor
@Service
public class PaymentEventListener {

    private final Logger logger = LoggerFactory.getLogger(PaymentEventListener.class);
    
    private final PaymentNotificationEventProducer paymentNotificationEventProducer;
    private LimitationService limitationService;
    
    @Async
    @EventListener(BillPaymentEvent.class)
 //   @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void onPaymentCreatedNotificationEvent(BillPaymentEvent billPaymentEvent) {    	
		PaymentNotificationEvent paymentNotificationEvent = new PaymentNotificationEvent();
		
		paymentNotificationEvent.setCreatedBy(billPaymentEvent.getPaymentDTO().getCreatedBy());
		paymentNotificationEvent.setBranchCode(billPaymentEvent.getPaymentDTO().getBranchCode());
		paymentNotificationEvent.setChannelCode(billPaymentEvent.getPaymentDTO().getChannelCode());
		paymentNotificationEvent.setChannelSessionId(billPaymentEvent.getPaymentDTO().getChannelSessionId());
		paymentNotificationEvent.setChannelTransactionId(billPaymentEvent.getPaymentDTO().getChannelTransactionId());
		paymentNotificationEvent.setInstitutionId(billPaymentEvent.getPaymentDTO().getInstitutionId());
		paymentNotificationEvent.setPaymentNotificationId(billPaymentEvent.getPaymentNotificationId());
		paymentNotificationEvent.setProductCode(billPaymentEvent.getInstitutionDTO().getProduct().getCode());
		paymentNotificationEvent.setInstitutionCode(billPaymentEvent.getInstitutionDTO().getInstitutionCode());
		
		if (paymentNotificationEventProducer == null) {
            logger.error("[onPaymentCreatedNotificationEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
		
        paymentNotificationEventProducer.sendPaymentNotificationEvent(paymentNotificationEvent);
    }
    
	@Async
	@EventListener(BillPaymentCancelEvent.class)
	public void onPaymentCancelCreatedNotificationEvent(BillPaymentCancelEvent event) {
		PaymentCancelNotificationEvent notifyCancelPaymentEvent = new PaymentCancelNotificationEvent();
		notifyCancelPaymentEvent.setCreatedBy(event.getCancelRecord().getCreatedBy());
		notifyCancelPaymentEvent.setBranchCode(event.getCancelRecord().getBranchCode());
		notifyCancelPaymentEvent.setChannelCode(event.getCancelRecord().getChannelCode());
		notifyCancelPaymentEvent.setChannelSessionId(event.getCancelRecord().getChannelSessionId());
		notifyCancelPaymentEvent.setChannelTransactionId(event.getCancelRecord().getChannelTransactionId());
		notifyCancelPaymentEvent.setInstitutionId(event.getInstitution().getId());
		notifyCancelPaymentEvent.setPaymentNotificationId(event.getPaymentNotificationId());
		notifyCancelPaymentEvent.setProductCode(event.getInstitution().getProduct().getCode());
		notifyCancelPaymentEvent.setInstitutionCode(event.getInstitution().getInstitutionCode());
		
		if (paymentNotificationEventProducer == null) {
            logger.error("[onPaymentCreatedNotificationEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
		
		paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(notifyCancelPaymentEvent);

	}
	
    @Async
    @EventListener(CreditCardProvisionACKEventDTO.class)
    public void onCreditCardProvisionACKEvent(CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO) {
        if (paymentNotificationEventProducer == null) {
            logger.error("[onCreditCardProvisionACKEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(creditCardProvisionACKEventDTO);
    }
    
    @Async
    @EventListener(CreditCardProvisionReverseEventDTO.class)
    public void onCreditCardProvisionReverseEvent(CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO) {
        if (paymentNotificationEventProducer == null) {
            logger.error("[onCreditCardProvisionReverseEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(creditCardProvisionReverseEventDTO);
    }
    
    @Async
    @EventListener(NotifyPaymentLimitationRequest.class)
	//@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
	public void onNotifyPaymentLimitation(NotifyPaymentLimitationRequest request) {
    	limitationService = SpringUtil.getBean(LimitationService.class);    	
    	limitationService.notifyPaymentLimitation(request);
	}

    @Async
    @EventListener(NotifyInquiryLimitationRequest.class)	
	public void onNotifyPaymentLimitation(NotifyInquiryLimitationRequest request) {
    	limitationService = SpringUtil.getBean(LimitationService.class);
		limitationService.notifyInquiryLimitation(request);
	}
    
    
}

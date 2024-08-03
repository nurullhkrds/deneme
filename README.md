@Component
@ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
@RequiredArgsConstructor
public class BillEventConsumer {

	private final PaymentService paymentService;
	
    private final PaymentNotificationService paymentNotificationService;
    
    private static final Logger logger = LoggerFactory.getLogger(BillEventConsumer.class);



	@RabbitListener(queues = "${rabbitmq.services.billtransaction-rabbitmq.queues.paymentNotificationEvent.name}", containerFactory = "paymentNotificationRabbitListenerContainerFactory")
	public void onMessage(final PaymentNotificationEvent paymentNotificationEvent) throws Exception {

		try {
			RequestContextHolder.setRequestAttributes(new CustomRequestScopeAttribute());

			NotifyPaymentProcessOutput result = paymentService.notifyPayment(paymentNotificationEvent);

			if (!Arrays.asList(EnumBillResult.SUCCESS, EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED)
					.contains(result.getResult())) {
				// tekrar denesin
				throw new RuntimeException("Notification is unsuccessfull");
			}

		} finally {
			RequestContextHolder.resetRequestAttributes();
		}

	}

	@RabbitListener(queues = "${rabbitmq.services.billtransaction-rabbitmq.queues.paymentCancelNotificationEvent.name}", containerFactory = "paymentCancelNotificationRabbitListenerContainerFactory")
	public void onMessage(final PaymentCancelNotificationEvent event) throws Exception {

		try {
			RequestContextHolder.setRequestAttributes(new CustomRequestScopeAttribute());

			NotifyPaymentCancelProcessOutput result = paymentService.notifyPaymentCancel(event);

			if (!Arrays.asList(EnumBillResult.SUCCESS, EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED)
					.contains(result.getResult())) {
				// tekrar denesin
				throw new RuntimeException("Notification is unsuccessfull");
			}

		} finally {
			RequestContextHolder.resetRequestAttributes();
		}

	}
	
	@RabbitListener(queues = "${rabbitmq.services.billtransaction-rabbitmq.queues.creditCardProvisionACKEvent.name}", containerFactory = "creditCardProvisionACKRabbitListenerContainerFactory")
	public void processCreditCardProvisionACKMessage(final CreditCardProvisionACKEventDTO event)
			throws BusinessException, JsonProcessingException {

		try {

			RequestContextHolder.setRequestAttributes(new CustomRequestScopeAttribute());

			paymentNotificationService.sendAckForCreditCardProvision(event.getPaymentId(),event.getPaymentNotificationId(), Boolean.FALSE);

			logger.info("Processed credit card provision ack message. Payment Id: '{}'", event.getPaymentId());

		} catch (Exception ex) {
			LogUtils.logError(logger, "Error occurred while processing received credit card provision ack message."
					+ " Exception Detail: '{}'", ExceptionUtils.getStackTrace(ex));
			throw ex;
		}

	}
	
	@RabbitListener(queues = "${rabbitmq.services.billtransaction-rabbitmq.queues.creditCardProvisionReverseEvent.name}", containerFactory = "creditCardReverseProvisionRabbitListenerContainerFactory")
	public void processCreditCardProvisionReverseMessage(final CreditCardProvisionReverseEventDTO event)
			throws BusinessException, JsonProcessingException {

		try {

			RequestContextHolder.setRequestAttributes(new CustomRequestScopeAttribute());

			paymentNotificationService.creditCardReverseProvision(event.getPaymentId(),event.getPaymentNotificationId(),event.getPaymentCancelId(),Boolean.FALSE);

			logger.info("Processed credit card reverse provision message. Payment Cancel Id: '{}'", event.getPaymentCancelId());

		} catch (Exception ex) {
			LogUtils.logError(logger, "Error occurred while processing received credit card reverse provision ack message."
					+ " Exception Detail: '{}'", ExceptionUtils.getStackTrace(ex));
			throw ex;
		}

	}
}

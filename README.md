@Component
@RequiredArgsConstructor
public class PaymentNotificationEventProducer {

    private static final Logger logger = LoggerFactory.getLogger(PaymentNotificationEventProducer.class);

    private final RabbitTemplate rabbitTemplate;


    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.paymentNotificationEvent.exchangeName}")
    private String paymentNotificationEventExchangeName;
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.paymentNotificationEvent.routingKey}")
    private String paymentNotificationEventRoutingKey;

    
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.paymentCancelNotificationEvent.exchangeName}")
    private String paymentCancelNotificationEventExchangeName;
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.paymentCancelNotificationEvent.routingKey}")
    private String paymentCancelNotificationEventRoutingKey;
    
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.creditCardProvisionACKEvent.exchangeName}")
    private String creditCardProvisionACKEventExchangeName;
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.creditCardProvisionACKEvent.routingKey}")
    private String creditCardProvisionACKEventRoutingKey;
    
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.creditCardProvisionReverseEvent.exchangeName}")
    private String creditCardProvisionReverseEventExchangeName;
    @Value("${rabbitmq.services.billtransaction-rabbitmq.producers.creditCardProvisionReverseEvent.routingKey}")
    private String ccreditCardProvisionReverseEventRoutingKey;
  
    @Async
    public void sendPaymentNotificationEvent(PaymentNotificationEvent paymentNotificationEvent) {
        try {
            rabbitTemplate.convertAndSend(paymentNotificationEventExchangeName, paymentNotificationEventRoutingKey, paymentNotificationEvent);

            logger.info("Published payment notification message." +
                            " paymentNotificationId: '{}'" ,
                            paymentNotificationEvent.getPaymentNotificationId());

        } catch (Exception ex) {
            logger.error("Error occurred while publishing payment notification message." +
                            " paymentNotificationId: '{}'" +                         
                            " Exception Detail: '{}'",
                            paymentNotificationEvent.getPaymentNotificationId(),
                    ExceptionUtils.getStackTrace(ex));
        }

    }
    
	@Async
	public void sendPaymentCancelNotificationEvent(PaymentCancelNotificationEvent paymentCancelNotificationEvent) {

		try {
			rabbitTemplate.convertAndSend(paymentCancelNotificationEventExchangeName,
					paymentCancelNotificationEventRoutingKey, paymentCancelNotificationEvent);
			logger.info("Published payment notification message." + " paymentNotificationId: '{}'",
					paymentCancelNotificationEvent.getPaymentNotificationId());

		} catch (Exception e) {
			logger.error(
					"Error occurred while publishing payment notification message." + " paymentNotificationId: '{}'"
							+ " Exception Detail: '{}'",
					paymentCancelNotificationEvent.getPaymentNotificationId(), ExceptionUtils.getStackTrace(e));
		}

	}
	
    public void sendCreditCardProvisionACKEvent(CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO) {
        try {
           
            rabbitTemplate.convertAndSend(creditCardProvisionACKEventExchangeName, creditCardProvisionACKEventRoutingKey, creditCardProvisionACKEventDTO);

            logger.info("Published credit card provision ack message. Payment Id: '{}'", creditCardProvisionACKEventDTO.getPaymentId());

        } catch (Exception ex) {
            logger.error("Error occurred while publishing credit card provision ack message." +
                            " Payment Id: '{}'" +
                            " Exception Detail: '{}'",
                    creditCardProvisionACKEventDTO.getPaymentId(),
                    ExceptionUtils.getStackTrace(ex));
        }
    }
    
	public void sendCreditCardProvisionReverseEvent(CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO) {
		try {

			rabbitTemplate.convertAndSend(creditCardProvisionReverseEventExchangeName,
					ccreditCardProvisionReverseEventRoutingKey, creditCardProvisionReverseEventDTO);

			logger.info("Published credit card provision reverse message. Payment Cancel Id: '{}'",
					creditCardProvisionReverseEventDTO.getPaymentCancelId());

		} catch (Exception ex) {
			logger.error(
					"Error occurred while publishing credit card provision ack message." + " Payment Cancel Id: '{}'"
							+ " Exception Detail: '{}'",
					creditCardProvisionReverseEventDTO.getPaymentCancelId(), ExceptionUtils.getStackTrace(ex));
		}
	}

}

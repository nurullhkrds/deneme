@EnableRabbit
@ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
@Configuration
@RequiredArgsConstructor
public class BillTransactionRabbitMQConfig {

    private static final Logger logger = LoggerFactory.getLogger(BillTransactionRabbitMQConfig.class);

    private static final String SERVICE_KEY = "billtransaction-rabbitmq";

    private static final String PAYMENT_NOTIFICATION_EVENT = "paymentNotificationEvent";
    private static final String PAYMENT_CANCEL_NOTIFICATION_EVENT = "paymentCancelNotificationEvent";
    private static final String CREDIT_CARD_PROVISION_ACK_EVENT = "creditCardProvisionACKEvent";
    private static final String CREDIT_CARD_REVERSE_PROVISION_EVENT = "creditCardProvisionReverseEvent";
    
	@Value("${parameter.creditCardProvisionACKEvent.parameters.notificationMaxTryCount:3}")
	private Integer creditCardProvisionACKNotificationMaxTryCount;
	
	@Value("${parameter.creditCardReverseProvisionEvent.parameters.notificationMaxTryCount:3}")
	private Integer creditCardReverseProvisionNotificationMaxTryCount;

    
    private static final String DEFAULT_USERNAME = "test";

	private static final String DEFAULT_PWORD = "test";

	private static final int DEFAULT_PORT = 5672;

	private static final String DEFAULT_HOST = "127.0.0.1";

    private final RabbitMQProperties rabbitMQProperties; 

   @Bean
    @Primary
    public RabbitTemplate billTransactionRabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate();
        rabbitTemplate.setConnectionFactory(connectionFactory);
        rabbitTemplate.setMessageConverter(jsonMessageConverter());
        rabbitTemplate.setChannelTransacted(false);
        declareQueues(connectionFactory);
        return rabbitTemplate;
    }




    private void declareQueues(ConnectionFactory connectionFactory) {
        if (rabbitMQProperties.getServiceByKey(SERVICE_KEY).getQueues() == null) {
            return;
        }

        try (Channel channel = RabbitMQUtil.getChannel(connectionFactory)) {
            rabbitMQProperties.getServiceByKey(SERVICE_KEY).getQueues().entrySet().stream().filter(spec -> spec.getValue().isDeclare()).forEach(spec -> declareQueue(channel, spec.getValue()));
        } catch (IOException | TimeoutException e) {
            logger.error("An error occurred while declaring queues.", e);
        }
    }
   private void declareQueue(Channel channel, QueueSpec queue) {
        if (!RabbitMQUtil.isValidExchangeType(queue)) {
            logger.error("Exchange type is wrong for queue. QueueName: {}, ExchangeName: {}", queue.getName(), queue.getExchange().getName());
            return;
        }

        try {
            callQueueDeclare(channel, queue);
        } catch (IOException e) {
            logger.error("An error occurred while declaring queue. QueueName: {}, ", queue.getName(), e);
        }
    }
    private void callQueueDeclare(Channel channel, QueueSpec queue) throws IOException {
        try {
            AMQP.Queue.DeclareOk response = channel.queueDeclarePassive(queue.getName());
            logger.info("Queue already declared. QueueName: {}", queue.getName());
            logger.info("QueueName: {}, Ready message count: {}, Consumer count: {}", queue.getName(), response.getMessageCount(), response.getConsumerCount());
        } catch (IOException e) {
            logger.error("Queue not declared. QueueName: {}, ", queue.getName(), e);

            channel.exchangeDeclare(queue.getExchange().getName(), queue.getExchange().getType(), queue.getExchange().isDurable());
            logger.info("Exchange Declared. QueueName: {}, ExchangeName: {}", queue.getName(), queue.getExchange().getName());

            channel.queueDeclare(queue.getName(), queue.isDurable(), false, false, queue.getArguments());
            logger.info("Queue Declared. QueueName: {}, ExchangeName: {}", queue.getName(), queue.getExchange().getName());

            channel.queueBind(queue.getName(), queue.getExchange().getName(), queue.getRoutingKey());
            logger.info("{} queue bound to {} exchange by {} rooting key.", queue.getName(), queue.getExchange().getName(), queue.getRoutingKey());
        }
    }

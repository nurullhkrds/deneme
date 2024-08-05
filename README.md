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
	@ConditionalOnProperty(value = "runtime.platform", havingValue = "pcf")
    public ConnectionFactory billTransactionRabbitFactory() {

        final CfService rabbitService = new CfEnv().findServiceByName(rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName());
        final CfCredentials rabbitCredentials = rabbitService.getCredentials();

        final String plan = rabbitService.getPlan();

        final String name = rabbitCredentials.getName();
        final String hostname = rabbitCredentials.getHost();
        final String username = rabbitCredentials.getUsername();
        final String password = rabbitCredentials.getPassword();
        final String virtualHost = rabbitCredentials.getString("vhost");

        final CachingConnectionFactory connectionFactory = new CachingConnectionFactory();
        connectionFactory.setAddresses(hostname);
        connectionFactory.setUsername(username);
        connectionFactory.setPassword(password);
        connectionFactory.setVirtualHost(virtualHost);

        logger.info("RabbitConnectionFactory [billTransactionRabbitFactory] is initialized with Pcf configuration " +
                        "ServiceName: [{}] Name: [{}] Hostname: [{}] VirtualHost: [{}] Username: [{}] Plan: [{}]",
                rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName(), name, hostname, virtualHost, username, plan);

        return connectionFactory;

    }
    
	@Bean
	@Primary
	@ConditionalOnProperty(value = "runtime.platform", havingValue = "local")
	public ConnectionFactory billRabbitLocalConnectionFactory() {
		CachingConnectionFactory connectionFactory = new CachingConnectionFactory();
		connectionFactory.setHost(DEFAULT_HOST);
		connectionFactory.setPort(DEFAULT_PORT);
		connectionFactory.setUsername(DEFAULT_USERNAME);
		connectionFactory.setPassword(DEFAULT_PWORD);
		return connectionFactory;
	}
    

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

    @Bean
    @Primary
    public MessageConverter jsonMessageConverter() {
        ObjectMapper mapper = new ObjectMapper().findAndRegisterModules();
        JavaTimeModule javaTimeModule = new JavaTimeModule();
        mapper.registerModule(javaTimeModule);
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        return new Jackson2JsonMessageConverter(mapper);
    }

    @Bean
    @ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
    public SimpleRabbitListenerContainerFactory paymentNotificationRabbitListenerContainerFactory(ConnectionFactory connectionFactory) {

        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();

        factory.setConnectionFactory(connectionFactory);
        factory.setMessageConverter(jsonMessageConverter());

        ConsumerSpec consumerSpec = rabbitMQProperties
                .getConsumerByKey(rabbitMQProperties.getServiceByKey(SERVICE_KEY), PAYMENT_NOTIFICATION_EVENT);

        factory.setConcurrentConsumers(consumerSpec.getMinConcurrentConsumers());
        factory.setMaxConcurrentConsumers(consumerSpec.getMaxConcurrentConsumers());
        factory.setPrefetchCount(consumerSpec.getPrefetchCount());
        factory.setAdviceChain(RetryInterceptorBuilder.stateless().maxAttempts(3).backOffOptions(500, 2, 1500).build());

        return factory;

    }
    
    @Bean
    @ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
    public SimpleRabbitListenerContainerFactory paymentCancelNotificationRabbitListenerContainerFactory(ConnectionFactory connectionFactory) {

        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();

        factory.setConnectionFactory(connectionFactory);
        factory.setMessageConverter(jsonMessageConverter());

        ConsumerSpec consumerSpec = rabbitMQProperties
                .getConsumerByKey(rabbitMQProperties.getServiceByKey(SERVICE_KEY), PAYMENT_CANCEL_NOTIFICATION_EVENT);

        factory.setConcurrentConsumers(consumerSpec.getMinConcurrentConsumers());
        factory.setMaxConcurrentConsumers(consumerSpec.getMaxConcurrentConsumers());
        factory.setPrefetchCount(consumerSpec.getPrefetchCount());
        factory.setAdviceChain(RetryInterceptorBuilder.stateless().maxAttempts(3).backOffOptions(500, 2, 1500).build());

        return factory;

    }
     
    @Bean
    @ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
    public SimpleRabbitListenerContainerFactory creditCardProvisionACKRabbitListenerContainerFactory(ConnectionFactory connectionFactory) {

        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();

        factory.setConnectionFactory(connectionFactory);
        factory.setMessageConverter(jsonMessageConverter());

        ConsumerSpec consumerSpec = rabbitMQProperties
                .getConsumerByKey(rabbitMQProperties.getServiceByKey(SERVICE_KEY), CREDIT_CARD_PROVISION_ACK_EVENT);

        factory.setConcurrentConsumers(consumerSpec.getMinConcurrentConsumers());
        factory.setMaxConcurrentConsumers(consumerSpec.getMaxConcurrentConsumers());
        factory.setPrefetchCount(consumerSpec.getPrefetchCount());
        factory.setAdviceChain(RetryInterceptorBuilder.stateless().maxAttempts(creditCardProvisionACKNotificationMaxTryCount).backOffOptions(500, 2, 1500).build());


        return factory;

    }
    
    @Bean
    @ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.billtransaction-rabbitmq.enabled:false}")
    public SimpleRabbitListenerContainerFactory creditCardReverseProvisionRabbitListenerContainerFactory(ConnectionFactory connectionFactory) {

        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();

        factory.setConnectionFactory(connectionFactory);
        factory.setMessageConverter(jsonMessageConverter());

        ConsumerSpec consumerSpec = rabbitMQProperties
                .getConsumerByKey(rabbitMQProperties.getServiceByKey(SERVICE_KEY), CREDIT_CARD_REVERSE_PROVISION_EVENT);

        factory.setConcurrentConsumers(consumerSpec.getMinConcurrentConsumers());
        factory.setMaxConcurrentConsumers(consumerSpec.getMaxConcurrentConsumers());
        factory.setPrefetchCount(consumerSpec.getPrefetchCount());
        factory.setAdviceChain(RetryInterceptorBuilder.stateless().maxAttempts(creditCardReverseProvisionNotificationMaxTryCount).backOffOptions(500, 2, 1500).build());


        return factory;

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

}

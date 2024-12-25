Caused by: java.lang.IllegalArgumentException: No service with name [PAYMENTS.BILL-TST.RabbitMQ] was found.
  services:
    bill-rabbitmq:
      name: PAYMENTS.BILL-TST.RabbitMQ
      enabled: true
      consumers:
        remoteLogEvent:
          minConcurrentConsumers: 4
          maxConcurrentConsumers: 8
          prefetchCount: 20
      queues:
        remoteLogEvent:
          declare: true
          name: remote-log-queue
          durable: true
          routingKey: remote-log-event
          arguments:
            x-message-ttl: 300000
          exchange:
            name: bill-direct-exchange
            durable: true
            type: direct




@Configuration
@ConditionalOnExpression("${rabbitmq.enabled:false} and ${rabbitmq.services.bill-rabbitmq.enabled:false}")
@EnableRabbit
@RequiredArgsConstructor
public class RabbitMQConfig {
	private static final Logger logger = LoggerFactory.getLogger(RabbitMQConfig.class);

	private static final String SERVICE_KEY = "bill-rabbitmq";

	private static final String REMOTE_LOG_EVET = "remoteLogEvent";
	
	private static final String DEFAULT_USERNAME = "guest";
	
	private static final String DEFAULT_PSW = "guest";

	private static final int DEFAULT_PORT = 5672;

	private static final String DEFAULT_HOST = "127.0.0.1";

	private final RabbitMQProperties rabbitMQProperties;

	@Bean
	@Primary
	@ConditionalOnProperty(value = "runtime.platform", havingValue = "pcf")
	public ConnectionFactory billRabbitPcfConnectionFactory() {
		//rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName()
		
		final CfService rabbitService = new CfEnv()
				.findServiceByName(rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName());
		final CfCredentials rabbitCredentials = rabbitService.getCredentials();

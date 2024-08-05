@ExtendWith(SpringExtension.class)
@SpringBootTest
class BillTransactionRabbitMQConfigTest {

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ConnectionFactory mockConnectionFactory;

    @InjectMocks
    private BillTransactionRabbitMQConfig billTransactionRabbitMQConfig;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testBillTransactionRabbitFactory() {
        CfService mockService = mock(CfService.class);
        CfCredentials mockCredentials = mock(CfCredentials.class);

        RabbitMQProperties.ServiceSpec serviceSpec = new RabbitMQProperties.ServiceSpec();
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(mockService.getCredentials()).thenReturn(mockCredentials);
        when(mockCredentials.getName()).thenReturn("mockName");
        when(mockCredentials.getHost()).thenReturn("mockHost");
        when(mockCredentials.getUsername()).thenReturn("mockUsername");
        when(mockCredentials.getPassword()).thenReturn("mockPassword");
        when(mockCredentials.getString(anyString())).thenReturn("mockVHost");

        CachingConnectionFactory connectionFactory = (CachingConnectionFactory) billTransactionRabbitMQConfig.billTransactionRabbitFactory();

        assertNotNull(connectionFactory);
        assertEquals("mockHost", connectionFactory.getHost());
        assertEquals("mockUsername", connectionFactory.getUsername());
        assertEquals("mockPassword", connectionFactory.getPassword());
        assertEquals("mockVHost", connectionFactory.getVirtualHost());
    }

    @Test
    void testBillRabbitLocalConnectionFactory() {
        CachingConnectionFactory connectionFactory = (CachingConnectionFactory) billTransactionRabbitMQConfig.billRabbitLocalConnectionFactory();

        assertNotNull(connectionFactory);
        assertEquals("127.0.0.1", connectionFactory.getHost());
        assertEquals(5672, connectionFactory.getPort());
        assertEquals("test", connectionFactory.getUsername());
        assertEquals("test", connectionFactory.getPassword());
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        RabbitTemplate rabbitTemplate = billTransactionRabbitMQConfig.billTransactionRabbitTemplate(mockConnectionFactory);

        assertNotNull(rabbitTemplate);
        assertEquals(mockConnectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testJsonMessageConverter() {
        MessageConverter messageConverter = billTransactionRabbitMQConfig.jsonMessageConverter();

        assertNotNull(messageConverter);
        assertTrue(messageConverter instanceof Jackson2JsonMessageConverter);

        ObjectMapper mapper = ((Jackson2JsonMessageConverter) messageConverter).getJsonObjectMapper();
        assertTrue(mapper.isEnabled(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS));
    }

    @Test
    void testPaymentNotificationRabbitListenerContainerFactory() {
        ConsumerSpec consumerSpec = new ConsumerSpec();
        consumerSpec.setMinConcurrentConsumers(1);
        consumerSpec.setMaxConcurrentConsumers(5);
        consumerSpec.setPrefetchCount(10);

        RabbitMQProperties.ServiceSpec serviceSpec = mock(RabbitMQProperties.ServiceSpec.class);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(rabbitMQProperties.getConsumerByKey(serviceSpec, "paymentNotificationEvent")).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentNotificationRabbitListenerContainerFactory(mockConnectionFactory);

        assertNotNull(factory);
        assertEquals(mockConnectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testPaymentCancelNotificationRabbitListenerContainerFactory() {
        ConsumerSpec consumerSpec = new ConsumerSpec();
        consumerSpec.setMinConcurrentConsumers(2);
        consumerSpec.setMaxConcurrentConsumers(10);
        consumerSpec.setPrefetchCount(20);

        RabbitMQProperties.ServiceSpec serviceSpec = mock(RabbitMQProperties.ServiceSpec.class);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(rabbitMQProperties.getConsumerByKey(serviceSpec, "paymentCancelNotificationEvent")).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentCancelNotificationRabbitListenerContainerFactory(mockConnectionFactory);

        assertNotNull(factory);
        assertEquals(mockConnectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardProvisionACKRabbitListenerContainerFactory() {
        ConsumerSpec consumerSpec = new ConsumerSpec();
        consumerSpec.setMinConcurrentConsumers(3);
        consumerSpec.setMaxConcurrentConsumers(15);
        consumerSpec.setPrefetchCount(30);

        RabbitMQProperties.ServiceSpec serviceSpec = mock(RabbitMQProperties.ServiceSpec.class);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(rabbitMQProperties.getConsumerByKey(serviceSpec, "creditCardProvisionACKEvent")).thenReturn(consumerSpec);

        billTransactionRabbitMQConfig.creditCardProvisionACKNotificationMaxTryCount = 3;

        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardProvisionACKRabbitListenerContainerFactory(mockConnectionFactory);

        assertNotNull(factory);
        assertEquals(mockConnectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        ConsumerSpec consumerSpec = new ConsumerSpec();
        consumerSpec.setMinConcurrentConsumers(4);
        consumerSpec.setMaxConcurrentConsumers(20);
        consumerSpec.setPrefetchCount(40);

        RabbitMQProperties.ServiceSpec serviceSpec = mock(RabbitMQProperties.ServiceSpec.class);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(rabbitMQProperties.getConsumerByKey(serviceSpec, "creditCardProvisionReverseEvent")).thenReturn(consumerSpec);

        billTransactionRabbitMQConfig.creditCardReverseProvisionNotificationMaxTryCount = 4;

        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardReverseProvisionRabbitListenerContainerFactory(mockConnectionFactory);

        assertNotNull(factory);
        assertEquals(mockConnectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testDeclareQueues() throws IOException, TimeoutException {
        Channel mockChannel = mock(Channel.class);
        QueueSpec mockQueueSpec = mock(QueueSpec.class);
        RabbitMQProperties.ServiceSpec mockServiceSpec = mock(RabbitMQProperties.ServiceSpec.class);

        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(mockServiceSpec);
        when(mockServiceSpec.getQueues()).thenReturn(Map.of("testQueue", mockQueueSpec));
        when(mockQueueSpec.isDeclare()).thenReturn(true);
        when(RabbitMQUtil.getChannel(any())).thenReturn(mockChannel);
        when(RabbitMQUtil.isValidExchangeType(any())).thenReturn(true);

        doNothing().when(mockChannel).exchangeDeclare(anyString(), anyString(), anyBoolean());
        doNothing().when(mockChannel).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        doNothing().when(mockChannel).queueBind(anyString(), anyString(), anyString());

        billTransactionRabbitMQConfig.declareQueues(mockConnectionFactory);

        verify(mockChannel, times(1)).exchangeDeclare(anyString(), anyString(), anyBoolean());
        verify(mockChannel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(mockChannel, times(1)).queueBind(anyString(), anyString(), anyString());
    }
}


    @BeforeEach
    void setUp() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getQueues()).thenReturn(Map.of("queueName", new QueueSpec()));
        ReflectionTestUtils.setField(config, "creditCardReverseProvisionNotificationMaxTryCount", 3); // Set the value here
    }

    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("creditCardProvisionReverseEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.creditCardReverseProvisionRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
    }

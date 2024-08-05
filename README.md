    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
        ServiceSpec serviceSpec = new ServiceSpec();
        serviceSpec.setQueues(new HashMap<>()); // Boş bir map ekliyoruz
        given(rabbitMQProperties.getServiceByKey("billtransaction-rabbitmq")).willReturn(serviceSpec);
    }

    @Test
    public void testBillTransactionRabbitTemplate() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertNotNull(rabbitTemplate.getMessageConverter());
    }

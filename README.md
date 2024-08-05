   @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ConnectionFactory connectionFactory;

    @InjectMocks
    private BillTransactionRabbitMQConfig billTransactionRabbitMQConfig;

    @Mock
    private ServiceSpec serviceSpec;

    @Mock
    private QueueSpec queueSpec;

    @BeforeEach
    void setUp() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getQueues()).thenReturn(Map.of("someQueue", queueSpec));
        when(queueSpec.isDeclare()).thenReturn(true);
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        RabbitTemplate rabbitTemplate = billTransactionRabbitMQConfig.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @InjectMocks
    private BillTransactionRabbitMQConfig config;

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ServiceSpec serviceSpec;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @Mock
    private ConsumerSpec consumerSpec;

    @Value("${parameter.creditCardProvisionACKEvent.parameters.notificationMaxTryCount:3}")
    private Integer creditCardProvisionACKNotificationMaxTryCount;

    @Value("${parameter.creditCardReverseProvisionEvent.parameters.notificationMaxTryCount:3}")
    private Integer creditCardReverseProvisionNotificationMaxTryCount;

    @BeforeEach
    void setUp() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getQueues()).thenReturn(Map.of("queueName", new QueueSpec()));
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);

        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

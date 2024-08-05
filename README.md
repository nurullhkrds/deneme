@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ConnectionFactory connectionFactory;

    @InjectMocks
    private BillTransactionRabbitMQConfig billTransactionRabbitMQConfig;

    @BeforeEach
    void setUp() {
        // Mock necessary properties if required
    }

    @Test
    void testBillTransactionRabbitFactory() {
        // Assuming the PCF configuration is set up correctly

        ConnectionFactory factory = billTransactionRabbitMQConfig.billTransactionRabbitFactory();
        assertNotNull(factory);
    }

    @Test
    void testBillRabbitLocalConnectionFactory() {
        ConnectionFactory factory = billTransactionRabbitMQConfig.billRabbitLocalConnectionFactory();
        assertNotNull(factory);
        assertTrue(factory instanceof CachingConnectionFactory);
        CachingConnectionFactory cachingConnectionFactory = (CachingConnectionFactory) factory;
        assertEquals("127.0.0.1", cachingConnectionFactory.getHost());
        assertEquals(5672, cachingConnectionFactory.getPort());
        assertEquals("test", cachingConnectionFactory.getUsername());
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        RabbitTemplate rabbitTemplate = billTransactionRabbitMQConfig.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testJsonMessageConverter() {
        MessageConverter converter = billTransactionRabbitMQConfig.jsonMessageConverter();
        assertNotNull(converter);
        assertTrue(converter instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testPaymentNotificationRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentNotificationRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
    }

    @Test
    void testPaymentCancelNotificationRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentCancelNotificationRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
    }

    @Test
    void testCreditCardProvisionACKRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardProvisionACKRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
    }

    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardReverseProvisionRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
    }
}

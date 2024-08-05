import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.amqp.core.MessageConverter;
import org.springframework.amqp.rabbit.connection.CachingConnectionFactory;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.listener.SimpleRabbitListenerContainerFactory;
import org.springframework.test.util.ReflectionTestUtils;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @InjectMocks
    private BillTransactionRabbitMQConfig billTransactionRabbitMQConfig;

    @Mock
    private ConnectionFactory connectionFactory;

    @Mock
    private CachingConnectionFactory cachingConnectionFactory;

    @Mock
    private RabbitTemplate rabbitTemplate;

    @Mock
    private SimpleRabbitListenerContainerFactory simpleRabbitListenerContainerFactory;

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(billTransactionRabbitMQConfig, "creditCardProvisionACKNotificationMaxTryCount", 3);
        ReflectionTestUtils.setField(billTransactionRabbitMQConfig, "creditCardReverseProvisionNotificationMaxTryCount", 3);
    }

    @Test
    void testBillTransactionRabbitFactory() {
        // Mock the necessary methods and return values
        // ... (Set up mocks for CfService, CfCredentials, etc.)

        ConnectionFactory factory = billTransactionRabbitMQConfig.billTransactionRabbitFactory();

        // Assert the properties of the factory
        assertNotNull(factory);
    }

    @Test
    void testBillRabbitLocalConnectionFactory() {
        ConnectionFactory factory = billTransactionRabbitMQConfig.billRabbitLocalConnectionFactory();

        assertNotNull(factory);
        assertEquals("127.0.0.1", ((CachingConnectionFactory) factory).getHost());
        assertEquals(5672, ((CachingConnectionFactory) factory).getPort());
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        when(connectionFactory.createConnection()).thenReturn(mock(com.rabbitmq.client.Connection.class));

        RabbitTemplate template = billTransactionRabbitMQConfig.billTransactionRabbitTemplate(connectionFactory);

        assertNotNull(template);
        assertEquals(connectionFactory, template.getConnectionFactory());
    }

    @Test
    void testJsonMessageConverter() {
        MessageConverter converter = billTransactionRabbitMQConfig.jsonMessageConverter();

        assertNotNull(converter);

        ObjectMapper mapper = ((Jackson2JsonMessageConverter) converter).getMapper();
        assertTrue(mapper.getRegisteredModuleIds().contains(JavaTimeModule.class.getName()));
        assertFalse(mapper.isEnabled(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS));
    }

    @Test
    void testPaymentNotificationRabbitListenerContainerFactory() {
        when(connectionFactory.createConnection()).thenReturn(mock(com.rabbitmq.client.Connection.class));

        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentNotificationRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
    }

    @Test
    void testDeclareQueues() throws IOException, TimeoutException {
        // Mock the necessary methods and return values
        Channel channel = mock(Channel.class);
        RabbitMQUtil utilMock = mock(RabbitMQUtil.class);

        // Set up mock for RabbitMQUtil.getChannel
        when(utilMock.getChannel(any(ConnectionFactory.class))).thenReturn(channel);

        billTransactionRabbitMQConfig.declareQueues(connectionFactory);

        // Verify that declareQueue was called
        verify(channel, times(1)).queueDeclarePassive(anyString());
    }

    @Test
    void testDeclareQueue() throws IOException {
        Channel channel = mock(Channel.class);
        QueueSpec queue = new QueueSpec();
        queue.setName("testQueue");
        queue.setExchange(new ExchangeSpec("testExchange", "direct", true));

        billTransactionRabbitMQConfig.declareQueue(channel, queue);

        verify(channel).exchangeDeclare(eq("testExchange"), eq("direct"), eq(true));
        verify(channel).queueDeclare(eq("testQueue"), eq(true), eq(false), eq(false), any());
        verify(channel).queueBind(eq("testQueue"), eq("testExchange"), eq(""));
    }
}

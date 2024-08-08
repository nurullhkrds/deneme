import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.io.IOException;
import java.util.Map;
import java.util.concurrent.TimeoutException;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.amqp.core.MessageConverter;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.connection.CachingConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.listener.SimpleRabbitListenerContainerFactory;
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.beans.factory.annotation.Value;

@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @InjectMocks
    private BillTransactionRabbitMQConfig config;

    @Mock
    private RabbitMQProperties rabbitMQProperties;

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
        // Set up mock properties here
    }

    @Test
    void testBillTransactionRabbitFactory() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getUsername()).thenReturn("user");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfCredentials.getString("vhost")).thenReturn("/");

        ConnectionFactory factory = config.billTransactionRabbitFactory();

        assertNotNull(factory);
        assertTrue(factory instanceof CachingConnectionFactory);
        CachingConnectionFactory cachingFactory = (CachingConnectionFactory) factory;
        assertEquals("localhost", cachingFactory.getHost());
        assertEquals("user", cachingFactory.getUsername());
    }

    @Test
    void testBillRabbitLocalConnectionFactory() {
        ConnectionFactory factory = config.billRabbitLocalConnectionFactory();

        assertNotNull(factory);
        assertTrue(factory instanceof CachingConnectionFactory);
        CachingConnectionFactory cachingFactory = (CachingConnectionFactory) factory;
        assertEquals("127.0.0.1", cachingFactory.getHost());
        assertEquals("test", cachingFactory.getUsername());
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);

        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testJsonMessageConverter() {
        MessageConverter converter = config.jsonMessageConverter();

        assertNotNull(converter);
        assertTrue(converter instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testPaymentNotificationRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("paymentNotificationEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.paymentNotificationRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testPaymentCancelNotificationRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("paymentCancelNotificationEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.paymentCancelNotificationRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardProvisionACKRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("creditCardProvisionACKEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.creditCardProvisionACKRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("creditCardProvisionReverseEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.creditCardReverseProvisionRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testDeclareQueues() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        Channel channel = mock(Channel.class);

        when(RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(cfService);
        when(cfService.getQueues()).thenReturn(Map.of("queueName", new QueueSpec()));

        config.declareQueues(connectionFactory);

        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
    }

    @Test
    void testDeclareQueue() throws IOException {
        Channel channel = mock(Channel.class);
        QueueSpec queueSpec = new QueueSpec();

        config.declareQueue(channel, queueSpec);

        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
    }

    @Test
    void testCallQueueDeclare() throws IOException {
        Channel channel = mock(Channel.class);
        QueueSpec queueSpec = new QueueSpec();

        doThrow(new IOException()).when(channel).queueDeclarePassive(anyString());

        config.callQueueDeclare(channel, queueSpec);

        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
    }
}

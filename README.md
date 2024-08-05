import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeoutException;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.amqp.core.MessageConverter;
import org.springframework.amqp.rabbit.connection.CachingConnectionFactory;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.listener.SimpleRabbitListenerContainerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.test.util.ReflectionTestUtils;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

public class BillTransactionRabbitMQConfigTest {

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @InjectMocks
    private BillTransactionRabbitMQConfig config;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testBillTransactionRabbitTemplate() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertNotNull(rabbitTemplate.getMessageConverter());
    }

    @Test
    public void testJsonMessageConverter() {
        MessageConverter converter = config.jsonMessageConverter();
        assertNotNull(converter);
        assertTrue(converter instanceof Jackson2JsonMessageConverter);
    }

    @Test
    public void testPaymentNotificationRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        ConsumerSpec consumerSpec = new ConsumerSpec();
        consumerSpec.setMinConcurrentConsumers(1);
        consumerSpec.setMaxConcurrentConsumers(10);
        consumerSpec.setPrefetchCount(5);

        when(rabbitMQProperties.getConsumerByKey(any(), anyString())).thenReturn(consumerSpec);
        SimpleRabbitListenerContainerFactory factory = config.paymentNotificationRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
    }

    @Test
    public void testDeclareQueues() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        Channel channel = mock(Channel.class);
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        queueSpec.setDurable(true);
        queueSpec.setExchange(new ExchangeSpec("testExchange", "direct", true));
        queueSpec.setRoutingKey("testRoutingKey");
        queueSpec.setDeclare(true);
        Map<String, QueueSpec> queues = new HashMap<>();
        queues.put("testQueue", queueSpec);

        RabbitMQProperties.ServiceSpec serviceSpec = new RabbitMQProperties.ServiceSpec();
        serviceSpec.setQueues(queues);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);

        try (MockedStatic<RabbitMQUtil> utilities = mockStatic(RabbitMQUtil.class)) {
            utilities.when(() -> RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);
            config.declareQueues(connectionFactory);
            verify(channel).queueDeclare(queueSpec.getName(), queueSpec.isDurable(), false, false, null);
            verify(channel).exchangeDeclare(queueSpec.getExchange().getName(), queueSpec.getExchange().getType(), queueSpec.getExchange().isDurable());
            verify(channel).queueBind(queueSpec.getName(), queueSpec.getExchange().getName(), queueSpec.getRoutingKey());
        }
    }
}

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
import org.mockito.MockedStatic;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.support.converter.MessageConverter;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.AMQP;

@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @InjectMocks
    private BillTransactionRabbitMQConfig config;

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ServiceSpec serviceSpec;

    @Mock
    private ConsumerSpec consumerSpec;

    @Mock
    private MessageConverter messageConverter;

    @Mock
    private ConnectionFactory connectionFactory;

    @Mock
    private Channel channel;

    @BeforeEach
    void setUp() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getQueues()).thenReturn(Map.of("queueName", new QueueSpec()));
    }

    @Test
    void testBillTransactionRabbitTemplate() throws Exception {
        // Mock the jsonMessageConverter method
        when(config.jsonMessageConverter()).thenReturn(messageConverter);

        // Mock the RabbitMQUtil.getChannel method to return a mock channel
        try (MockedStatic<RabbitMQUtil> mockedStatic = mockStatic(RabbitMQUtil.class)) {
            mockedStatic.when(() -> RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);

            // Mock the behavior of channel.queueDeclarePassive and other channel methods
            when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not declared"));
            doNothing().when(channel).exchangeDeclare(anyString(), anyString(), anyBoolean());
            doNothing().when(channel).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
            doNothing().when(channel).queueBind(anyString(), anyString(), anyString());

            RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

            assertNotNull(rabbitTemplate);
            assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
            assertEquals(messageConverter, rabbitTemplate.getMessageConverter());
            assertFalse(rabbitTemplate.isChannelTransacted());

            // Verify that the appropriate methods were called
            verify(channel, times(1)).queueDeclarePassive(anyString());
            verify(channel, times(1)).exchangeDeclare(anyString(), anyString(), anyBoolean());
            verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
            verify(channel, times(1)).queueBind(anyString(), anyString(), anyString());
        }
    }
}
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-inline</artifactId>
    <version>4.0.0</version>
    <scope>test</scope>
</dependency>

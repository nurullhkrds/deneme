import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.amqp.core.MessageConverter;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.listener.SimpleRabbitListenerContainerFactory;
import org.springframework.beans.factory.annotation.Value;

@ExtendWith(MockitoExtension.class)
public class BillTransactionRabbitMQConfigTest {

    @Mock
    private RabbitMQProperties rabbitMQProperties;

    @Mock
    private ConnectionFactory connectionFactory;

    @InjectMocks
    private BillTransactionRabbitMQConfig billTransactionRabbitMQConfig;

    @Mock
    private ServiceKeyConfig serviceKeyConfig;

    @Mock
    private QueueSpec queueSpec;

    @BeforeEach
    void setUp() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceKeyConfig);
        when(serviceKeyConfig.getQueues()).thenReturn(Map.of("someQueue", queueSpec));
        when(queueSpec.isDeclare()).thenReturn(true);
    }

    @Test
    void testBillTransactionRabbitTemplate() {
        RabbitTemplate rabbitTemplate = billTransactionRabbitMQConfig.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertTrue(rabbitTemplate.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }
    
    // Diğer test metotları burada
}

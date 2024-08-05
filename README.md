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

    @BeforeEach
    void setUp() {
        // Mock necessary properties if required
    }

    @Test
    void testBillTransactionRabbitFactory() {
        // Assuming the PCF configuration is set up correctly
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(new ServiceKeyConfig());
        
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
        assertEquals("test", cachingConnectionFactory.getPassword());
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
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testPaymentCancelNotificationRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.paymentCancelNotificationRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardProvisionACKRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardProvisionACKRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }

    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = billTransactionRabbitMQConfig.creditCardReverseProvisionRabbitListenerContainerFactory(connectionFactory);
        assertNotNull(factory);
        assertEquals(connectionFactory, factory.getConnectionFactory());
        assertTrue(factory.getMessageConverter() instanceof Jackson2JsonMessageConverter);
    }
}

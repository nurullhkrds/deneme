import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.amqp.rabbit.core.RabbitTemplate;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
class PaymentNotificationEventProducerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @InjectMocks
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    private static final String PAYMENT_NOTIFICATION_EXCHANGE = "paymentNotificationEventExchangeName";
    private static final String PAYMENT_NOTIFICATION_ROUTING_KEY = "paymentNotificationEventRoutingKey";
    private static final String PAYMENT_CANCEL_EXCHANGE = "paymentCancelNotificationEventExchangeName";
    private static final String PAYMENT_CANCEL_ROUTING_KEY = "paymentCancelNotificationEventRoutingKey";
    private static final String CREDIT_CARD_ACK_EXCHANGE = "creditCardProvisionACKEventExchangeName";
    private static final String CREDIT_CARD_ACK_ROUTING_KEY = "creditCardProvisionACKEventRoutingKey";
    private static final String CREDIT_CARD_REVERSE_EXCHANGE = "creditCardProvisionReverseEventExchangeName";
    private static final String CREDIT_CARD_REVERSE_ROUTING_KEY = "ccreditCardProvisionReverseEventRoutingKey";

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testSendPaymentNotificationEvent_Success() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId("123");

        doNothing().when(rabbitTemplate).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);

        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentNotificationEvent_Failure() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Success() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId("123");

        doNothing().when(rabbitTemplate).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);

        paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Failure() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event));

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Success() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("123");

        doNothing().when(rabbitTemplate).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);

        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Failure() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event));

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Success() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId("123");

        doNothing().when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }
}

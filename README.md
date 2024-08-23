import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.test.util.ReflectionTestUtils;

import static org.junit.jupiter.api.Assertions.assertDoesNotThrow;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

class PaymentNotificationEventProducerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @InjectMocks
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        // Set up the values for the properties
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventExchangeName", "payment.exchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventRoutingKey", "payment.routingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventExchangeName", "payment.cancel.exchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventRoutingKey", "payment.cancel.routingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventExchangeName", "creditCard.ack.exchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventRoutingKey", "creditCard.ack.routingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "ccreditCardProvisionReverseEventExchangeName", "creditCard.reverse.exchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "ccreditCardProvisionReverseEventRoutingKey", "creditCard.reverse.routingKey");
    }

    @Test
    void testSendPaymentNotificationEvent() {
        PaymentNotificationEvent paymentNotificationEvent = mock(PaymentNotificationEvent.class);
        when(paymentNotificationEvent.getPaymentNotificationId()).thenReturn("12345");

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentNotificationEvent(paymentNotificationEvent));

        verify(rabbitTemplate).convertAndSend("payment.exchange", "payment.routingKey", paymentNotificationEvent);
    }

    @Test
    void testSendPaymentCancelNotificationEvent() {
        PaymentCancelNotificationEvent paymentCancelNotificationEvent = mock(PaymentCancelNotificationEvent.class);
        when(paymentCancelNotificationEvent.getPaymentNotificationId()).thenReturn("54321");

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(paymentCancelNotificationEvent));

        verify(rabbitTemplate).convertAndSend("payment.cancel.exchange", "payment.cancel.routingKey", paymentCancelNotificationEvent);
    }

    @Test
    void testSendCreditCardProvisionACKEvent() {
        CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO = mock(CreditCardProvisionACKEventDTO.class);
        when(creditCardProvisionACKEventDTO.getPaymentId()).thenReturn("67890");

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(creditCardProvisionACKEventDTO));

        verify(rabbitTemplate).convertAndSend("creditCard.ack.exchange", "creditCard.ack.routingKey", creditCardProvisionACKEventDTO);
    }

    @Test
    void testSendCreditCardProvisionReverseEvent() {
        CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO = mock(CreditCardProvisionReverseEventDTO.class);
        when(creditCardProvisionReverseEventDTO.getPaymentCancelId()).thenReturn("09876");

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(creditCardProvisionReverseEventDTO));

        verify(rabbitTemplate).convertAndSend("creditCard.reverse.exchange", "creditCard.reverse.routingKey", creditCardProvisionReverseEventDTO);
    }
}

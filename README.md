import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;
import org.springframework.amqp.rabbit.core.RabbitTemplate;

import static org.junit.jupiter.api.Assertions.assertDoesNotThrow;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class PaymentNotificationEventProducerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @InjectMocks
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventExchangeName", "paymentNotificationEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventRoutingKey", "paymentNotificationEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventExchangeName", "paymentCancelNotificationEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventRoutingKey", "paymentCancelNotificationEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventExchangeName", "creditCardProvisionACKEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventRoutingKey", "creditCardProvisionACKEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionReverseEventExchangeName", "creditCardProvisionReverseEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionReverseEventRoutingKey", "creditCardProvisionReverseEventRoutingKey");
    }

    @Test
    void testSendPaymentNotificationEvent_Success() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("paymentNotificationEventExchange"), eq("paymentNotificationEventRoutingKey"), eq(event));
    }

    @Test
    void testSendPaymentNotificationEvent_Failure() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Success() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("paymentCancelNotificationEventExchange"), eq("paymentCancelNotificationEventRoutingKey"), eq(event));
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Failure() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event));
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Success() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionACKEventExchange"), eq("creditCardProvisionACKEventRoutingKey"), eq(event));
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Failure() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event));
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Success() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionReverseEventExchange"), eq("creditCardProvisionReverseEventRoutingKey"), eq(event));
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));
    }
}

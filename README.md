import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;

import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.amqp.rabbit.test.context.SpringRabbitTest;
import org.springframework.amqp.rabbit.test.mockito.RabbitListenerTest;
import org.springframework.amqp.test.context.SpringRabbitTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
@ExtendWith(SpringExtension.class)
@SpringRabbitTest
public class BillEventConsumerTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @InjectMocks
    private BillEventConsumer billEventConsumer;

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(billEventConsumer, "logger", mock(Logger.class));
    }

    @Test
    public void testOnMessage_PaymentNotificationEvent_Success() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentNotificationEvent_Failure() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.FAILURE);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
        verify(paymentService, times(1)).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentCancelNotificationEvent_Success() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentCancelNotificationEvent_Failure() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.FAILURE);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
        verify(paymentService, times(1)).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    public void testProcessCreditCardProvisionACKMessage_Success() throws Exception {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("paymentId");
        event.setPaymentNotificationId("paymentNotificationId");

        doNothing().when(paymentNotificationService).sendAckForCreditCardProvision(anyString(), anyString(), anyBoolean());

        billEventConsumer.processCreditCardProvisionACKMessage(event);

        verify(paymentNotificationService, times(1)).sendAckForCreditCardProvision(anyString(), anyString(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionReverseMessage_Success() throws Exception {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId("paymentId");
        event.setPaymentNotificationId("paymentNotificationId");
        event.setPaymentCancelId("paymentCancelId");

        doNothing().when(paymentNotificationService).creditCardReverseProvision(anyString(), anyString(), anyString(), anyBoolean());

        billEventConsumer.processCreditCardProvisionReverseMessage(event);

        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyString(), anyString(), anyString(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionACKMessage_Exception() throws Exception {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("paymentId");
        event.setPaymentNotificationId("paymentNotificationId");

        doThrow(new RuntimeException("Test Exception")).when(paymentNotificationService).sendAckForCreditCardProvision(anyString(), anyString(), anyBoolean());

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.processCreditCardProvisionACKMessage(event);
        });

        assertEquals("Test Exception", exception.getMessage());
        verify(paymentNotificationService, times(1)).sendAckForCreditCardProvision(anyString(), anyString(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionReverseMessage_Exception() throws Exception {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId("paymentId");
        event.setPaymentNotificationId("paymentNotificationId");
        event.setPaymentCancelId("paymentCancelId");

        doThrow(new RuntimeException("Test Exception")).when(paymentNotificationService).creditCardReverseProvision(anyString(), anyString(), anyString(), anyBoolean());

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.processCreditCardProvisionReverseMessage(event);
        });

        assertEquals("Test Exception", exception.getMessage());
        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyString(), anyString(), anyString(), anyBoolean());
    }
}

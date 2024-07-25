import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

import com.fasterxml.jackson.core.JsonProcessingException;

@ExtendWith(MockitoExtension.class)
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
    void testOnMessagePaymentNotificationEventSuccess() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    void testOnMessagePaymentNotificationEventFailure() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.FAILURE);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
    }

    @Test
    void testOnMessagePaymentCancelNotificationEventSuccess() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    void testOnMessagePaymentCancelNotificationEventFailure() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.FAILURE);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
    }

    @Test
    void testProcessCreditCardProvisionACKMessage() throws BusinessException, JsonProcessingException {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("123");
        event.setPaymentNotificationId("456");

        billEventConsumer.processCreditCardProvisionACKMessage(event);

        verify(paymentNotificationService).sendAckForCreditCardProvision("123", "456", Boolean.FALSE);
    }

    @Test
    void testProcessCreditCardProvisionReverseMessage() throws BusinessException, JsonProcessingException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId("123");
        event.setPaymentNotificationId("456");
        event.setPaymentCancelId("789");

        billEventConsumer.processCreditCardProvisionReverseMessage(event);

        verify(paymentNotificationService).creditCardReverseProvision("123", "456", "789", Boolean.FALSE);
    }
}

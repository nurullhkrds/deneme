import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.web.context.request.RequestContextHolder;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

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
        RequestContextHolder.resetRequestAttributes();
    }

    @Test
    void testOnMessagePaymentNotificationEvent() throws Exception {
        // Given
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        // When
        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        // Then
        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    void testOnMessagePaymentCancelNotificationEvent() throws Exception {
        // Given
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        // When
        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        // Then
        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    void testProcessCreditCardProvisionACKMessage() throws Exception {
        // Given
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);
        event.setPaymentNotificationId(456L);

        // When
        doNothing().when(paymentNotificationService).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());

        // Then
        billEventConsumer.processCreditCardProvisionACKMessage(event);

        verify(paymentNotificationService, times(1)).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());
    }

    @Test
    void testProcessCreditCardProvisionReverseMessage() throws Exception {
        // Given
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId(123L);
        event.setPaymentNotificationId(456L);
        event.setPaymentCancelId(789L);

        // When
        doNothing().when(paymentNotificationService).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());

        // Then
        billEventConsumer.processCreditCardProvisionReverseMessage(event);

        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());
    }
}

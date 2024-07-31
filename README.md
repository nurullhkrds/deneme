import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.test.util.ReflectionTestUtils;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class PaymentEventListenerTest {

    @Mock
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @Mock
    private LimitationService limitationService;

    @InjectMocks
    private PaymentEventListener paymentEventListener;

    private static final Logger logger = LoggerFactory.getLogger(PaymentEventListener.class);

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(paymentEventListener, "logger", logger);
    }

    @Test
    void testOnPaymentCreatedNotificationEvent() {
        BillPaymentEvent billPaymentEvent = mock(BillPaymentEvent.class);
        // Mock billPaymentEvent and its methods
        // For example:
        PaymentDTO paymentDTO = mock(PaymentDTO.class);
        when(billPaymentEvent.getPaymentDTO()).thenReturn(paymentDTO);
        when(paymentDTO.getCreatedBy()).thenReturn("user");

        paymentEventListener.onPaymentCreatedNotificationEvent(billPaymentEvent);

        verify(paymentNotificationEventProducer, times(1))
                .sendPaymentNotificationEvent(any(PaymentNotificationEvent.class));
    }

    @Test
    void testOnPaymentCancelCreatedNotificationEvent() {
        BillPaymentCancelEvent event = mock(BillPaymentCancelEvent.class);
        // Mock event and its methods

        paymentEventListener.onPaymentCancelCreatedNotificationEvent(event);

        verify(paymentNotificationEventProducer, times(1))
                .sendPaymentCancelNotificationEvent(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    void testOnCreditCardProvisionACKEvent() {
        CreditCardProvisionACKEventDTO event = mock(CreditCardProvisionACKEventDTO.class);

        paymentEventListener.onCreditCardProvisionACKEvent(event);

        verify(paymentNotificationEventProducer, times(1))
                .sendCreditCardProvisionACKEvent(event);
    }

    @Test
    void testOnCreditCardProvisionReverseEvent() {
        CreditCardProvisionReverseEventDTO event = mock(CreditCardProvisionReverseEventDTO.class);

        paymentEventListener.onCreditCardProvisionReverseEvent(event);

        verify(paymentNotificationEventProducer, times(1))
                .sendCreditCardProvisionReverseEvent(event);
    }

    @Test
    void testOnNotifyPaymentLimitation() {
        NotifyPaymentLimitationRequest request = mock(NotifyPaymentLimitationRequest.class);

        when(limitationService.notifyPaymentLimitation(request)).thenReturn(null);
        ReflectionTestUtils.setField(paymentEventListener, "limitationService", limitationService);

        paymentEventListener.onNotifyPaymentLimitation(request);

        verify(limitationService, times(1)).notifyPaymentLimitation(request);
    }

    @Test
    void testOnNotifyInquiryLimitation() {
        NotifyInquiryLimitationRequest request = mock(NotifyInquiryLimitationRequest.class);

        when(limitationService.notifyInquiryLimitation(request)).thenReturn(null);
        ReflectionTestUtils.setField(paymentEventListener, "limitationService", limitationService);

        paymentEventListener.onNotifyPaymentLimitation(request);

        verify(limitationService, times(1)).notifyInquiryLimitation(request);
    }
}

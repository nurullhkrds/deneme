import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.test.context.junit.jupiter.SpringExtension;

@ExtendWith(MockitoExtension.class)
public class PaymentEventListenerTest {

    @Mock
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @Mock
    private LimitationService limitationService;

    @InjectMocks
    private PaymentEventListener paymentEventListener;

    private BillPaymentEvent billPaymentEvent;
    private BillPaymentCancelEvent billPaymentCancelEvent;
    private CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO;
    private CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO;
    private NotifyPaymentLimitationRequest notifyPaymentLimitationRequest;
    private NotifyInquiryLimitationRequest notifyInquiryLimitationRequest;

    @BeforeEach
    void setUp() {
        // Mock event objects
        billPaymentEvent = mock(BillPaymentEvent.class);
        billPaymentCancelEvent = mock(BillPaymentCancelEvent.class);
        creditCardProvisionACKEventDTO = mock(CreditCardProvisionACKEventDTO.class);
        creditCardProvisionReverseEventDTO = mock(CreditCardProvisionReverseEventDTO.class);
        notifyPaymentLimitationRequest = mock(NotifyPaymentLimitationRequest.class);
        notifyInquiryLimitationRequest = mock(NotifyInquiryLimitationRequest.class);

        // Mock other necessary methods and behaviors
        when(billPaymentEvent.getPaymentDTO()).thenReturn(mock(PaymentDTO.class));
        when(billPaymentEvent.getInstitutionDTO()).thenReturn(mock(InstitutionDTO.class));
    }

    @Test
    void testOnPaymentCreatedNotificationEvent() {
        paymentEventListener.onPaymentCreatedNotificationEvent(billPaymentEvent);

        verify(paymentNotificationEventProducer, times(1)).sendPaymentNotificationEvent(any(PaymentNotificationEvent.class));
    }

    @Test
    void testOnPaymentCancelCreatedNotificationEvent() {
        paymentEventListener.onPaymentCancelCreatedNotificationEvent(billPaymentCancelEvent);

        verify(paymentNotificationEventProducer, times(1)).sendPaymentCancelNotificationEvent(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    void testOnCreditCardProvisionACKEvent() {
        paymentEventListener.onCreditCardProvisionACKEvent(creditCardProvisionACKEventDTO);

        verify(paymentNotificationEventProducer, times(1)).sendCreditCardProvisionACKEvent(any(CreditCardProvisionACKEventDTO.class));
    }

    @Test
    void testOnCreditCardProvisionReverseEvent() {
        paymentEventListener.onCreditCardProvisionReverseEvent(creditCardProvisionReverseEventDTO);

        verify(paymentNotificationEventProducer, times(1)).sendCreditCardProvisionReverseEvent(any(CreditCardProvisionReverseEventDTO.class));
    }

    @Test
    void testOnNotifyPaymentLimitation() {
        paymentEventListener.onNotifyPaymentLimitation(notifyPaymentLimitationRequest);

        verify(limitationService, times(1)).notifyPaymentLimitation(any(NotifyPaymentLimitationRequest.class));
    }

    @Test
    void testOnNotifyInquiryLimitation() {
        paymentEventListener.onNotifyPaymentLimitation(notifyInquiryLimitationRequest);

        verify(limitationService, times(1)).notifyInquiryLimitation(any(NotifyInquiryLimitationRequest.class));
    }
}

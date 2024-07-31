import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.context.ApplicationEventPublisher;

import java.util.Optional;
import java.util.Collections;
import java.util.List;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class PaymentEventPublisherTest {

    @Mock
    private ApplicationEventPublisher eventPublisher;

    @InjectMocks
    private PaymentEventPublisher paymentEventPublisher;

    private PublishPaymentTypeDTO publishPaymentTypeDTO;
    private PaymentNotificationDTO paymentNotificationDTO;
    private PaymentDTO paymentDTO;
    private InstitutionDTO institutionDTO;
    private PaymentCancelDTO paymentCancelDTO;

    @BeforeEach
    public void setup() {
        paymentDTO = new PaymentDTO();
        institutionDTO = new InstitutionDTO();
        paymentNotificationDTO = new PaymentNotificationDTO();
        paymentCancelDTO = new PaymentCancelDTO();
        publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setInstitutionDTO(institutionDTO);
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Collections.singletonList(paymentNotificationDTO));
    }

    @Test
    public void testFindPublishPaymentEvent() {
        // Test for INSTITUTION_PAYMENT_NOTIFICATION
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
            .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));

        // Test for CRD_PRVSN_ACK
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        paymentDTO.setPaymentMethod(new PaymentMethod(EnumProvisionType.CARD));
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
            .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }

    @Test
    public void testFindPublishPaymentEventV2() {
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
            .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEventV2(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));
    }

    @Test
    public void testFindPublishPaymentCancelEvent() {
        // Test for INSTITUTION_CANCEL_NOTIFICATION
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
            .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentCancelEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentCancelEvent.class));

        // Test for RVRS_PROVISION
        paymentNotificationDTO.setNotificationType(EnumPaymentNotificationType.RVRS_PROVISION);
        paymentDTO.setPaymentMethod(new PaymentMethod(EnumProvisionType.CARD));
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
            .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentCancelEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionReverseEventDTO.class));
    }

    @Test
    public void testPublishPaymentNotificationEvent() {
        paymentEventPublisher.publishPaymentNotificationEvent(paymentDTO, institutionDTO, 1L);
        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));
    }

    @Test
    public void testPublishPaymentCancelNotificationEvent() {
        paymentEventPublisher.publishPaymentCancelNotificationEvent(paymentDTO, paymentCancelDTO, institutionDTO, 1L);
        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentCancelEvent.class));
    }

    @Test
    public void testPublishCreditCardProvisionACKEvent() {
        paymentEventPublisher.publishCreditCardProvisionACKEvent(1L, 1L);
        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }

    @Test
    public void testPublishCreditCardProvisionReverseEvent() {
        paymentEventPublisher.publishCreditCardProvisionReverseEvent(paymentDTO, paymentCancelDTO, 1L);
        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionReverseEventDTO.class));
    }

    @Test
    public void testPublishPaymentLimiationNotification() {
        NotifyPaymentLimitationRequest notifyPaymentLimitationRequest = new NotifyPaymentLimitationRequest();
        paymentEventPublisher.publishPaymentLimiationNotification(notifyPaymentLimitationRequest);
        verify(eventPublisher, times(1)).publishEvent(notifyPaymentLimitationRequest);
    }

    @Test
    public void testPublishInquiryLimiationNotification() {
        NotifyInquiryLimitationRequest notifyInquiryLimitationRequest = new NotifyInquiryLimitationRequest();
        paymentEventPublisher.publishInquiryLimiationNotification(notifyInquiryLimitationRequest);
        verify(eventPublisher, times(1)).publishEvent(notifyInquiryLimitationRequest);
    }
}

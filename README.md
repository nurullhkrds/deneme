import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.Collections;
import java.util.Optional;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.context.ApplicationEventPublisher;

public class PaymentEventPublisherTest {

    @Mock
    private ApplicationEventPublisher eventPublisher;

    @InjectMocks
    private PaymentEventPublisher paymentEventPublisher;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testFindPublishPaymentEvent_withInstitutionPaymentNotification() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = createPublishPaymentTypeDTO(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        ArgumentCaptor<BillPaymentEvent> eventCaptor = ArgumentCaptor.forClass(BillPaymentEvent.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        BillPaymentEvent event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(publishPaymentTypeDTO.getPaymentDTO(), event.getPaymentDTO());
        assertEquals(publishPaymentTypeDTO.getInstitutionDTO(), event.getInstitutionDTO());
    }

    @Test
    void testFindPublishPaymentEvent_withCardProvisionACK() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = createPublishPaymentTypeDTO(EnumPaymentNotificationType.CRD_PRVSN_ACK, EnumProvisionType.CARD);
        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        ArgumentCaptor<CreditCardProvisionACKEventDTO> eventCaptor = ArgumentCaptor.forClass(CreditCardProvisionACKEventDTO.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        CreditCardProvisionACKEventDTO event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(publishPaymentTypeDTO.getPaymentDTO().getId(), event.getPaymentId());
    }

    @Test
    void testFindPublishPaymentCancelEvent_withInstitutionCancelNotification() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = createPublishPaymentTypeDTO(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION);
        paymentEventPublisher.findPublishPaymentCancelEvent(publishPaymentTypeDTO);

        ArgumentCaptor<BillPaymentCancelEvent> eventCaptor = ArgumentCaptor.forClass(BillPaymentCancelEvent.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        BillPaymentCancelEvent event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(publishPaymentTypeDTO.getPaymentDTO(), event.getCancelledPayment());
        assertEquals(publishPaymentTypeDTO.getInstitutionDTO(), event.getInstitution());
    }

    @Test
    void testPublishPaymentNotificationEvent() {
        PaymentDTO paymentDTO = new PaymentDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        Long notificationId = 1L;

        paymentEventPublisher.publishPaymentNotificationEvent(paymentDTO, institutionDTO, notificationId);

        ArgumentCaptor<BillPaymentEvent> eventCaptor = ArgumentCaptor.forClass(BillPaymentEvent.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        BillPaymentEvent event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(paymentDTO, event.getPaymentDTO());
        assertEquals(institutionDTO, event.getInstitutionDTO());
        assertEquals(notificationId, event.getPaymentNotificationId());
    }

    @Test
    void testPublishCreditCardProvisionACKEvent() {
        Long paymentId = 1L;
        Long notificationId = 2L;

        paymentEventPublisher.publishCreditCardProvisionACKEvent(paymentId, notificationId);

        ArgumentCaptor<CreditCardProvisionACKEventDTO> eventCaptor = ArgumentCaptor.forClass(CreditCardProvisionACKEventDTO.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        CreditCardProvisionACKEventDTO event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(paymentId, event.getPaymentId());
        assertEquals(notificationId, event.getPaymentNotificationId());
    }

    // Add other test cases for remaining methods

    private PublishPaymentTypeDTO createPublishPaymentTypeDTO(EnumPaymentNotificationType notificationType) {
        return createPublishPaymentTypeDTO(notificationType, EnumProvisionType.OTHER);
    }

    private PublishPaymentTypeDTO createPublishPaymentTypeDTO(EnumPaymentNotificationType notificationType, EnumProvisionType provisionType) {
        PaymentNotificationDTO notificationDTO = new PaymentNotificationDTO();
        notificationDTO.setNotificationType(notificationType);
        
        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setPaymentMethod(new PaymentMethodDTO(provisionType));

        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Collections.singletonList(notificationDTO));
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setInstitutionDTO(new InstitutionDTO());

        return publishPaymentTypeDTO;
    }
}

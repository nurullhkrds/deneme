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

    @Mock
    private PublishPaymentTypeDTO publishPaymentTypeDTO;

    @Mock
    private PaymentNotificationDTO paymentNotificationDTO;

    @Mock
    private PaymentDTO paymentDTO;

    @Mock
    private InstitutionDTO institutionDTO;

    @Mock
    private PaymentCancelDTO paymentCancelDTO;

    @BeforeEach
    public void setup() {
        when(publishPaymentTypeDTO.getPaymentDTO()).thenReturn(paymentDTO);
        when(publishPaymentTypeDTO.getInstitutionDTO()).thenReturn(institutionDTO);
    }

    @Test
    public void testFindPublishPaymentEvent() {
        // Test for INSTITUTION_PAYMENT_NOTIFICATION
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));

        // Test for CRD_PRVSN_ACK
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        when(paymentDTO.getPaymentMethod())
                .thenReturn(new PaymentMethod(EnumProvisionType.CARD));
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }
}

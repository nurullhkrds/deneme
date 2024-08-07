import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
public class NotifyPaymentProcessTest {

    @InjectMocks
    private NotifyPaymentProcess notifyPaymentProcess;

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @BeforeEach
    public void setUp() {
        // Mock nesneleri ve NotifyPaymentProcess nesnesini ayarla
        notifyPaymentProcess = new NotifyPaymentProcess();
        notifyPaymentProcess.paymentService = paymentService;
        notifyPaymentProcess.paymentNotificationService = paymentNotificationService;
        notifyPaymentProcess.adapterService = adapterService;
    }

    @Test
    public void testExecuteProcess() throws BillException {
        // Mock veri hazırlığı
        Long paymentNotificationId = 1L;
        Long paymentId = 1L;
        PaymentNotification paymentNotification = new PaymentNotification();
        paymentNotification.setPaymentId(paymentId);
        paymentNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.PENDING);
        paymentNotification.setRetryCount(0);

        Payment payment = new Payment();
        payment.setCreateDate(LocalDateTime.now());

        when(paymentNotificationService.findPaymentNotificationWithLock(paymentNotificationId)).thenReturn(paymentNotification);
        when(paymentService.findPaymentByIdWithLock(paymentId)).thenReturn(payment);
        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn("3");

        // Test edilen method
        notifyPaymentProcess.initProcess(new ProcessExecutionInput(), new ProcessLogDTO());
        notifyPaymentProcess.executeProcess();

        // Beklenen sonuçlar ve doğrulamalar
        assertNotNull(notifyPaymentProcess.executionOutput);
        assertEquals(EnumBillResult.SUCCESS, notifyPaymentProcess.executionOutput.getResult());

        verify(paymentNotificationService, times(1)).findPaymentNotificationWithLock(paymentNotificationId);
        verify(paymentService, times(1)).findPaymentByIdWithLock(paymentId);
        verify(adapterService, times(1)).notifyPayment(any(), any(), any());
    }
}

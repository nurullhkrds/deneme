import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.jupiter.MockitoExtension;

import java.time.LocalDateTime;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyLong;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class NotifyPaymentProcessTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @InjectMocks
    private NotifyPaymentProcess process;

    @Mock
    private ProcessExecutionInput input;

    @Mock
    private ProcessLogDTO logDTO;

    @Mock
    private PaymentNotification paymentNotification;

    @Mock
    private Payment payment;

    @Mock
    private NotifyPaymentAdapterResponse notifyPaymentResponse;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        when(institutionFeatureService.getFeatureValue(any(), anyString(), anyString()))
                .thenReturn(BillTransactionConstant.DEFAULT_NOTIFICATION_TRY_COUNT.toString());
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(paymentNotification);
        when(paymentService.findPaymentByIdWithLock(anyLong())).thenReturn(payment);
        when(adapterService.notifyPayment(any(), anyString(), anyString())).thenReturn(notifyPaymentResponse);

        when(paymentNotification.getNotificationType()).thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(paymentNotification.getNotificationStatus()).thenReturn(EnumPaymentNotificationStatu.WAITING);
        when(paymentNotification.getRetryCount()).thenReturn(0);
        when(paymentNotification.getPaymentId()).thenReturn(1L);

        when(payment.getInstitutionDebtTypeId()).thenReturn(1L);
        when(payment.getCreateDate()).thenReturn(LocalDateTime.now());

        when(notifyPaymentResponse.getStatus()).thenReturn(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        when(notifyPaymentResponse.getNotifiedBill()).thenReturn(null);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertNull(process.getExecutionOutput().getResult());
        verify(paymentNotificationService).updatePaymentNotification(any());
        verify(paymentService).updatePayment(any());
    }

    @Test
    void testExecuteProcessNotificationNotFound() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(null);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcessPaymentNotFound() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(paymentNotification);
        when(paymentNotification.getNotificationType()).thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(paymentNotification.getNotificationStatus()).thenReturn(EnumPaymentNotificationStatu.WAITING);
        when(paymentNotification.getRetryCount()).thenReturn(0);
        when(paymentNotification.getPaymentId()).thenReturn(1L);
        when(paymentService.findPaymentByIdWithLock(anyLong())).thenReturn(null);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcessExceededRetryCount() throws BillException {
        when(institutionFeatureService.getFeatureValue(any(), anyString(), anyString()))
                .thenReturn(BillTransactionConstant.DEFAULT_NOTIFICATION_TRY_COUNT.toString());
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(paymentNotification);
        when(paymentNotification.getNotificationType()).thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(paymentNotification.getNotificationStatus()).thenReturn(EnumPaymentNotificationStatu.WAITING);
        when(paymentNotification.getRetryCount()).thenReturn(BillTransactionConstant.DEFAULT_NOTIFICATION_TRY_COUNT.intValue() + 1);
        when(paymentNotification.getPaymentId()).thenReturn(1L);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_TRY_COUNT_EXCEEDED, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcessAlreadyNotified() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(paymentNotification);
        when(paymentNotification.getNotificationType()).thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(paymentNotification.getNotificationStatus()).thenReturn(EnumPaymentNotificationStatu.SUCCESS);
        when(paymentNotification.getRetryCount()).thenReturn(0);
        when(paymentNotification.getPaymentId()).thenReturn(1L);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED, process.getExecutionOutput().getResult());
    }

    @Test
    void testExecuteProcessInvalidNotificationType() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(paymentNotification);
        when(paymentNotification.getNotificationType()).thenReturn(EnumPaymentNotificationType.OTHER_NOTIFICATION);
        when(paymentNotification.getNotificationStatus()).thenReturn(EnumPaymentNotificationStatu.WAITING);
        when(paymentNotification.getRetryCount()).thenReturn(0);
        when(paymentNotification.getPaymentId()).thenReturn(1L);

        process.setDataPack(new HashMap<>());
        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_TYPE_INVALID, process.getExecutionOutput().getResult());
    }
}

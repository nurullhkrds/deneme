import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyLong;
import static org.mockito.Mockito.*;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.MockitoJUnitRunner;
import static org.junit.Assert.*;

@RunWith(MockitoJUnitRunner.class)
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
    private NotifyPaymentProcess notifyPaymentProcess;

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testGatherData() throws BillException {
        NotifyPaymentProcess.GatherData gatherDataStep = notifyPaymentProcess.new GatherData();

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn("3");

        gatherDataStep.executeStep();

        assertNotNull(notifyPaymentProcess.notificationTryCount);
        assertNotNull(notifyPaymentProcess.paymentNotificationService);
        assertNotNull(notifyPaymentProcess.paymentService);
        assertNotNull(notifyPaymentProcess.adapterService);
    }

    @Test
    public void testFetchPaymentNotificationRecordWithLock() throws BillException {
        NotifyPaymentProcess.FetchPaymentNotificationRecordWithLock step = notifyPaymentProcess.new FetchPaymentNotificationRecordWithLock();

        PaymentNotification mockNotification = new PaymentNotification();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
        mockNotification.setRetryCount(0);
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(mockNotification);

        notifyPaymentProcess.paymentNotificationId = 1L;
        step.executeStep();

        assertNotNull(notifyPaymentProcess.paymentNotification);
    }

    @Test
    public void testFetchPaymentRecordWithLock() throws BillException {
        NotifyPaymentProcess.FetchPaymentRecordWithLock step = notifyPaymentProcess.new FetchPaymentRecordWithLock();

        Payment mockPayment = new Payment();
        when(paymentService.findPaymentByIdWithLock(anyLong())).thenReturn(mockPayment);

        notifyPaymentProcess.paymentId = 1L;
        step.executeStep();

        assertNotNull(notifyPaymentProcess.payment);
    }

    @Test
    public void testCallInstitutionExternalService() throws BillException {
        NotifyPaymentProcess.CallInstitutionExternalService step = notifyPaymentProcess.new CallInstitutionExternalService();

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        when(adapterService.notifyPayment(any(), any(), any())).thenReturn(mockResponse);

        Payment mockPayment = new Payment();
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setInstitutionDebtTypeId(1L);
        notifyPaymentProcess.payment = mockPayment;

        step.executeStep();

        assertNotNull(notifyPaymentProcess.notifyPaymentResponse);
    }

    @Test
    public void testUpdatePaymentNotificationRecord() throws BillException {
        NotifyPaymentProcess.UpdatePaymentNotificationRecord step = notifyPaymentProcess.new UpdatePaymentNotificationRecord();

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        notifyPaymentProcess.notifyPaymentResponse = mockResponse;

        PaymentNotification mockNotification = new PaymentNotification();
        notifyPaymentProcess.paymentNotification = mockNotification;

        step.executeStep();

        verify(paymentNotificationService).updatePaymentNotification(any(PaymentNotification.class));
    }

    @Test
    public void testUpdatePaymentRecord() throws BillException {
        NotifyPaymentProcess.UpdatePaymentRecord step = notifyPaymentProcess.new UpdatePaymentRecord();

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        NotifiedBillAdapterDTO notifiedBill = new NotifiedBillAdapterDTO();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        mockResponse.setNotifiedBill(notifiedBill);
        notifyPaymentProcess.notifyPaymentResponse = mockResponse;

        Payment mockPayment = new Payment();
        notifyPaymentProcess.payment = mockPayment;

        step.executeStep();

        verify(paymentService).updatePayment(any(Payment.class));
    }

    // Diğer dahili sınıfların testleri de benzer şekilde yazılabilir.
}

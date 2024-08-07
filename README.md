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

    private Long institutionDebtTypeId;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        process.setDataPack(new HashMap<>());
        process.setExecutionOutput(new NotifyPaymentProcessOutput());
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

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
        process.initProcess(input, logDTO);
        process.executeProcess();

        assertNull(process.getExecutionOutput().getResult());
        verify(paymentNotificationService).updatePaymentNotification(any());
        verify(paymentService).updatePayment(any());
    }

    @Test
    void testExecuteProcessNotificationNotFound() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(anyLong())).thenReturn(null);

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
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

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
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

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
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

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
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

        process.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.NOTIFY_PAYMENT);
        process.initProcess(input, logDTO);
        process.executeProcess();

        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_TYPE_INVALID, process.getExecutionOutput().getResult());
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {
        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}

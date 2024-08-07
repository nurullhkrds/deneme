
public class NotifyPaymentProcessTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @InjectMocks
    private NotifyPaymentProcess process;

    @Mock
    private ProcessService processService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        
        // Manually setting dependencies
        process.paymentService = paymentService;
        process.paymentNotificationService = paymentNotificationService;
        process.adapterService = adapterService;
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn("3");
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification());
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment());
        when(adapterService.notifyPayment(any(NotifyPaymentAdapterRequest.class), anyString(), anyString())).thenReturn(mockNotifyPaymentAdapterResponse());

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNotNull(output);
        assertEquals(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS, output.getResult());
    }

    @Test
    void testExecuteProcessPaymentNotificationNotFound() throws BillException {
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(null);
        
        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLog"));
        process.executeProcess();

        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNotNull(output);
        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND, output.getResult());
    }

    private PaymentNotification mockNotification() {
        PaymentNotification notification = new PaymentNotification();
        notification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        notification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        notification.setPaymentId(1L);
        notification.setRetryCount(0);
        return notification;
    }

    private Payment mockPayment() {
        Payment payment = new Payment();
        payment.setInstitutionDebtTypeId(1L);
        payment.setCreateDate(LocalDateTime.now());
        payment.setAdditionalInfo1("Info1");
        return payment;
    }

    private NotifyPaymentAdapterResponse mockNotifyPaymentAdapterResponse() {
        NotifyPaymentAdapterResponse response = new NotifyPaymentAdapterResponse();
        response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        NotifiedBillAdapterDTO notifiedBill = new NotifiedBillAdapterDTO();
        response.setNotifiedBill(notifiedBill);
        return response;
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {

        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}

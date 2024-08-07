class NotifyPaymentProcessTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @Mock
    private ProcessService processService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @InjectMocks
    private NotifyPaymentProcess process;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Mocking ProcessService
        InstitutionDebtTypeDTO mockInstitutionDebtType = new InstitutionDebtTypeDTO();
        mockInstitutionDebtType.setId(123L);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any()))
            .thenReturn(mockInstitutionDebtType);

        // Mocking PaymentNotificationService
        PaymentNotification mockNotification = new PaymentNotification();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        mockNotification.setPaymentId(1L);
        mockNotification.setRetryCount(0);
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification);

        // Mocking PaymentService
        Payment mockPayment = new Payment();
        mockPayment.setInstitutionDebtTypeId(123L);
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setAdditionalInfo1("Info1");
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);

        // Mocking AdapterService
        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        mockResponse.setNotifiedBill(new NotifiedBillAdapterDTO());
        when(adapterService.notifyPayment(any(), anyString(), anyString())).thenReturn(mockResponse);

        // Mocking InstitutionFeatureService
        when(institutionFeatureService.getFeatureValue(any(), anyString(), anyString()))
            .thenReturn("someFeatureValue");
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
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

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNull(output); // Or any other assertion based on expected behavior
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {

        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}

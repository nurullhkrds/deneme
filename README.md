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
    private ApplicationContext applicationContext;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @InjectMocks
    private NotifyPaymentProcess process;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        // Mocking SpringUtil setup
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);
        when(SpringUtil.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(SpringUtil.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(SpringUtil.getBean(PaymentService.class)).thenReturn(paymentService);
        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);

        // Mock data setup
        PaymentNotification mockNotification = new PaymentNotification();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        mockNotification.setPaymentId(1L);
        mockNotification.setRetryCount(0);

        Payment mockPayment = new Payment();
        mockPayment.setInstitutionDebtTypeId(1L);
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setAdditionalInfo1("Info1");

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        mockResponse.setNotifiedBill(new NotifiedBillAdapterDTO());

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);

        // Mock behavior setup
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification);
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);
        when(adapterService.notifyPayment(any(NotifyPaymentAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);

        // Test input and execution
        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        // Assertion
        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNull(output); // Adjust as needed based on expected output
    }

    @Test
    void testExecuteProcessPaymentNotificationNotFound() throws BillException {
        // Mocking SpringUtil setup
        when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);
        when(SpringUtil.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(SpringUtil.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(SpringUtil.getBean(PaymentService.class)).thenReturn(paymentService);
        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);

        // Mock behavior setup
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(null);

        // Test input and execution
        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLog"));
        process.executeProcess();

        // Assertion
        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNull(output); // Adjust as needed based on expected output
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {
        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}

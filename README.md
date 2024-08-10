@ExtendWith(MockitoExtension.class)
class BillPaymentReverseProcessTest {

    @Mock
    private ApplicationContext applicationContext;

    @Mock
    private AccountingService accountingService;

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @Mock
    private ChannelService channelService;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    private PaymentDTO paymentDTO;

    @Mock
    private InstitutionDTO institutionDTO;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        SpringUtil.setApplicationContext(applicationContext);

        // Mockların Spring context içinde doğru şekilde yer aldığından emin olun
        when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(applicationContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(applicationContext.getBean(PaymentService.class)).thenReturn(paymentService);
        when(applicationContext.getBean(AccountingService.class)).thenReturn(accountingService);
        when(applicationContext.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(applicationContext.getBean(ChannelService.class)).thenReturn(channelService);

        // Test verilerini başlatın
        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setChannelCode("someChannelCode");
        paymentDTO.setContractNo(456L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);

        institutionDTO = new InstitutionDTO();
        institutionDTO.setInstitutionCode("123");
        institutionDTO.setIsReverseAllowed(true);
        billPaymentReverseProcess.setInstitution(institutionDTO);

        // Reflection ile stepHandler başlatılması
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(billPaymentReverseProcess, billPaymentReverseProcess.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException, IllegalAccessException, NoSuchFieldException {
        // Stubbing
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);
        when(accountingService.doReverseAccounting(any())).thenReturn(createReverseAccountingResultDTO);
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

        // Veri paketi oluşturma
        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        // Testi çalıştırma
        billPaymentReverseProcess.executeProcess();

        // Doğrulamalar
        verify(paymentService).getPayment(1L, 456L);
        verify(accountingService).doReverseAccounting(any());
        verify(paymentNotificationService, times(1)).insertPaymentNotification(any());
        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());

        assertNotNull(billPaymentReverseProcess.getExecutionOutput());
    }
}

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
    private InstitutionDTO institutionDTO;

    @BeforeEach
    void setUp() throws Exception {
        MockitoAnnotations.openMocks(this);

        // Mock ApplicationContext
        when(applicationContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(applicationContext.getBean(PaymentService.class)).thenReturn(paymentService);
        when(applicationContext.getBean(AccountingService.class)).thenReturn(accountingService);
        when(applicationContext.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(applicationContext.getBean(ChannelService.class)).thenReturn(channelService);

        // SpringUtil.appContext'e ApplicationContext'i set et
        SpringUtil.setApplicationContext(applicationContext);

        // paymentDTO ve institutionDTO'yu başlat
        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setChannelCode("someChannelCode");
        paymentDTO.setContractNo(456L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);

        institutionDTO = new InstitutionDTO();
        institutionDTO.setInstitutionCode("123");
        institutionDTO.setIsReverseAllowed(true);

        billPaymentReverseProcess.setInstitution(institutionDTO);

        // Reflection kullanarak stepHandler'ı başlat
        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(billPaymentReverseProcess, billPaymentReverseProcess.new ProcessStepHandler());

        // Testte kullanılacak diğer mock nesneleri hazırlayın
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        // Adımları test edin
        billPaymentReverseProcess.executeProcess();

        // Verify adımlarının çağrıldığını doğrulayın
        verify(paymentService).getPayment(1L, 456L);
        verify(accountingService).doReverseAccounting(any());
        verify(paymentNotificationService, times(2)).insertPaymentNotification(any());
        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());

        // Assert sonuçlar
        assertEquals(EnumBillResult.SUCCESS, billPaymentReverseProcess.getExecutionOutput().getResult());
    }
}

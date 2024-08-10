@ExtendWith(MockitoExtension.class)
class BillPaymentReverseProcessTest {

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
    void setUp() {
        MockitoAnnotations.openMocks(this);

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

    @Test
    void testExecuteProcess_CheckIsReversible_Fails() throws BillException {
        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        // paymentDTO'nun channelCode'unu uyuşmayan bir değer yap
        paymentDTO.setChannelCode("differentChannelCode");

        // Adımları test edin
        billPaymentReverseProcess.executeProcess();

        // Verify adımlarının çağrılmadığını doğrulayın
        verify(accountingService, never()).doReverseAccounting(any());

        // Assert sonuçlar
        assertEquals(EnumBillResult.BILL_REVERSE_PAYMENT_CHANNEL_DIFFERENT_ERROR, billPaymentReverseProcess.getExecutionOutput().getResult());
    }
}

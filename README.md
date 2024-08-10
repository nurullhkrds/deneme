@ExtendWith(MockitoExtension.class)
public class BillPaymentReverseProcessTest {

    @Mock
    private AccountingServiceImpl accountingService;

    @Mock
    private PaymentServiceImpl paymentService;

    @Mock
    private PaymentNotificationServiceImpl paymentNotificationService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private InstitutionFeatureServiceImpl institutionFeatureService;

    @Mock
    private ChannelServiceImpl channelService;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    private PaymentDTO paymentDTO;
    private CreateReverseAccountingResultDTO createReverseAccountingResultDTO;
    private InstitutionDTO institutionDTO;

    @BeforeEach
    public void setUp() {
        // Setup your mock objects and common data here
        paymentDTO = new PaymentDTO();
        paymentDTO.setId(1L);
        paymentDTO.setChannelCode("TEST_CHANNEL");
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);
        paymentDTO.setContractNo(123L);
        
        institutionDTO = new InstitutionDTO();
        institutionDTO.setIsReverseAllowed(true);
        
        createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);

        billPaymentReverseProcess.setInstitution(institutionDTO);
        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 123L);
    }

    @Test
    public void testExecuteProcess_Success() throws BillException {
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        when(accountingService.doReverseAccounting(any(CreateReverseAccountingDTO.class))).thenReturn(createReverseAccountingResultDTO);

        billPaymentReverseProcess.executeProcess();

        verify(paymentService).getPayment(1L, 123L);
        verify(accountingService).doReverseAccounting(any(CreateReverseAccountingDTO.class));
        verify(paymentService).insertPaymentCancel(any(PaymentCancelDTO.class));
    }

    @Test
    public void testFetchBillRecord_Fail() throws BillException {
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(null);

        billPaymentReverseProcess.executeProcess();

        assertEquals(EnumBillResult.PAYMENT_NOT_FOUND, billPaymentReverseProcess.getExecutionOutput().getResult());
    }

    @Test
    public void testCheckIsReversible_Fail() throws BillException {
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        when(channelService.areChannelsTheSameAdkGroup(any(), any())).thenReturn(false);

        paymentDTO.setChannelCode("DIFFERENT_CHANNEL");

        billPaymentReverseProcess.executeProcess();

        assertEquals(EnumBillResult.BILL_REVERSE_PAYMENT_CHANNEL_DIFFERENT_ERROR, billPaymentReverseProcess.getExecutionOutput().getResult());
    }

    @Test
    public void testDoAccounting_Fail() throws BillException {
        createReverseAccountingResultDTO.setSuccess(false);
        createReverseAccountingResultDTO.setError(EnumBillResult.ACCOUNTING_ERROR);

        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        when(accountingService.doReverseAccounting(any(CreateReverseAccountingDTO.class))).thenReturn(createReverseAccountingResultDTO);

        billPaymentReverseProcess.executeProcess();

        assertEquals(EnumBillResult.ACCOUNTING_ERROR, billPaymentReverseProcess.getExecutionOutput().getResult());
    }

    @Test
    public void testInsertPaymentCancelNotificationRecord_Success() throws BillException {
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        when(accountingService.doReverseAccounting(any(CreateReverseAccountingDTO.class))).thenReturn(createReverseAccountingResultDTO);

        billPaymentReverseProcess.executeProcess();

        verify(paymentNotificationService, times(2)).insertPaymentNotification(any(PaymentNotificationDTO.class));
    }

    // Add more tests as needed for other steps

}

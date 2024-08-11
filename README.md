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

    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    private PaymentDTO paymentDTO;

    private ChannelDTO channelDTO;
    private List<PaymentNotificationDTO> insertedPaymentNotificationDTOList;



    @Mock
    private InstitutionDTO institutionDTO;

    @BeforeEach
    void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);
        SpringUtil springUtil=new SpringUtil();

        paymentUtilImpl=new PaymentUtilImpl(institutionFeatureService);
        springUtil.setApplicationContext(applicationContext);

        lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(applicationContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        lenient().when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        lenient().when(applicationContext.getBean(PaymentService.class)).thenReturn(paymentService);
        lenient().when(applicationContext.getBean(AccountingService.class)).thenReturn(accountingService);
        lenient().when(applicationContext.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        lenient().when(applicationContext.getBean(ChannelService.class)).thenReturn(channelService);

        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setChannelCode("someChannelCode");
        paymentDTO.setContractNo(456L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);


        ProcessLogDTO logDTO = new ProcessLogDTO("123");
        billPaymentReverseProcess.setLogDTO(logDTO);

         channelDTO=new ChannelDTO();
         channelDTO.setCode("123");

         insertedPaymentNotificationDTOList=new ArrayList<>();


        institutionDTO = new InstitutionDTO();
        institutionDTO.setInstitutionCode("123");
        institutionDTO.setIsReverseAllowed(true);
        billPaymentReverseProcess.setInstitution(institutionDTO);
        when(channelService.findChannelByChannelCode(any())).thenReturn(channelDTO);

        Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
        stepHandlerField.setAccessible(true);
        stepHandlerField.set(billPaymentReverseProcess, billPaymentReverseProcess.new ProcessStepHandler());
    }

    @Test
    void testExecuteProcess_Success() throws BillException {
        PaymentCancelDTO paymentCancelDTO=new PaymentCancelDTO();
        paymentCancelDTO.setPaymentId(123L);
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(insertedPaymentNotificationDTOList);
        publishPaymentTypeDTO.setInstitutionDTO(institutionDTO);
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setPaymentCancelDTO(paymentCancelDTO);
        when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);
        when(accountingService.doReverseAccounting(any())).thenReturn(createReverseAccountingResultDTO);
        when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);
        doNothing().when(paymentEventPublisher).findPublishPaymentEvent(publishPaymentTypeDTO);


        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

        billPaymentReverseProcess.executeProcess();

        verify(paymentService).getPayment(1L, 456L);
        verify(accountingService).doReverseAccounting(any());
        verify(paymentNotificationService, times(1)).insertPaymentNotification(any());
        verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());

        assertNull(billPaymentReverseProcess.getExecutionOutput());
    }
}
BUNU YAPTIM AMA HALA 
java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO.getProduct()" because "institution" is null
HATASI ALIYORUM

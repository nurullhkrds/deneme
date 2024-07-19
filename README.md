@Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ReverseProvisionFactory reverseProvisionFactory;

    @Mock
    private ReceiptService receiptService;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReverseProvisionService reverseProvisionService;

    @InjectMocks
    private AccountingServiceImpl accountingService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testDoAccounting_Success() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentMethodType(PaymentMethodType.CREDIT_CARD); // örnek veri
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(5));
        
        createAccountingDTO.getProvisionDTO().setCommissionData("{\"totalCommissionLocalCurrencyAmount\": 10, \"totalCommissionTaxLocalCurrencyAmount\": 5}");

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);
        createAccountingResultDTO.setPendingDetailList(null);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        verify(provisionFactory).getProvisionService(createAccountingDTO.getPaymentMethodType().getProvisionType());
        verify(provisionService).doAccounting(createAccountingDTO);
        verify(receiptService).printReceipt(createAccountingDTO, createAccountingResultDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(115), result.getTotalPaymentAmount());
        assertEquals(BigDecimal.valueOf(15), result.getCommissionAmount());
    }

    @Test
    void testDoReverseAccounting() {
        CreateReverseAccountingDTO createReverseAccountingDTO = new CreateReverseAccountingDTO();
        createReverseAccountingDTO.setPaymentMethodType(PaymentMethodType.CREDIT_CARD); // örnek veri

        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        createReverseAccountingResultDTO.setSuccess(true);

        when(reverseProvisionFactory.getReverseProvisionService(any())).thenReturn(reverseProvisionService);
        when(reverseProvisionService.doReverseAccounting(any())).thenReturn(createReverseAccountingResultDTO);

        CreateReverseAccountingResultDTO result = accountingService.doReverseAccounting(createReverseAccountingDTO);

        verify(reverseProvisionFactory).getReverseProvisionService(createReverseAccountingDTO.getPaymentMethodType().getProvisionType());
        verify(reverseProvisionService).doReverseAccounting(createReverseAccountingDTO);

        assertTrue(result.isSuccess());
    }

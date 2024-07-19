 @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReceiptService receiptService;

    @InjectMocks
    private AccountingService accountingService;

    private CreateAccountingDTO createAccountingDTO;
    private CreateAccountingResultDTO createAccountingResultDTO;

    @BeforeEach
    public void setUp() {
        createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.TEN);
        createAccountingDTO.setPaymentMethodType(new PaymentMethodType(ProvisionType.CREDIT_CARD));
        createAccountingDTO.setResponseCommissionInformation(new ResponseCommissionInformation(
            BigDecimal.ONE, BigDecimal.ONE
        ));

        createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);
    }

    @Test
    public void testDoAccounting_successful() {
        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(12), result.getTotalPaymentAmount());
        assertEquals(BigDecimal.valueOf(2), result.getCommissionAmount());
        verify(receiptService).print-IsNotReceipt(createAccountingDTO, createAccountingResultDTO);
    }

    @Test
    public void testDoAccounting_noCommissionInformation() {
        createAccountingDTO.setResponseCommissionInformation(null);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(10), result.getTotalPaymentAmount());
        assertEquals(BigDecimal.ZERO, result.getCommissionAmount());
        verify(receiptService).printReceipt(createAccountingDTO, createAccountingResultDTO);
    }

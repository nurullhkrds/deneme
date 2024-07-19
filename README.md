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
    private EnumPaymentMethod mockPaymentMethodType;
    private EnumProvisionType mockProvisionType;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        
        mockPaymentMethodType = EnumPaymentMethod.CREDIT_CARD;  // Or any valid enum value
        createAccountingDTO.setPaymentMethodType(mockPaymentMethodType);
        
        mockProvisionType = EnumProvisionType.SOME_TYPE;  // Or any valid enum value
        createAccountingDTO.getPaymentMethodType().setProvisionType(mockProvisionType);
        
        createAccountingResultDTO = new CreateAccountingResultDTO();
    }

    @Test
    void testDoAccounting_SuccessWithCommission() {
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(2));
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        createAccountingResultDTO.setSuccess(true);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(12), result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(112), result.getTotalPaymentAmount());
        verify(receiptService, times(1)).printReceipt(createAccountingDTO, createAccountingResultDTO);
    }

    @Test
    void testDoAccounting_SuccessWithoutCommission() {
        createAccountingResultDTO.setSuccess(true);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.ZERO, result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(100), result.getTotalPaymentAmount());
        verify(receiptService, times(1)).printReceipt(createAccountingDTO, createAccountingResultDTO);
    }

    @Test
    void testDoAccounting_Failure() {
        createAccountingResultDTO.setSuccess(false);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        verify(receiptService, times(0)).printReceipt(any(), any());
    }

    @Test
    void testDoAccounting_NullPaymentMethodType() {
        createAccountingDTO.setPaymentMethodType(null);  // Set PaymentMethodType as null

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        verify(provisionFactory, times(0)).getProvisionService(any());
        verify(provisionService, times(0)).doAccounting(any());
        verify(receiptService, times(0)).printReceipt(any(), any());
    }

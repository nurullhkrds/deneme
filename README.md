    @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReceiptService receiptService;

    private AccountingService accountingService;  // Bu bir arayüz, bu yüzden onu doğrudan @InjectMocks ile kullanamayız.

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        
        // Mock nesneleri gerçek bir sınıfa enjekte edin.
        AccountingServiceImpl accountingServiceImpl = new AccountingServiceImpl();
        accountingServiceImpl.setProvisionFactory(provisionFactory);
        accountingServiceImpl.set dueDateServices(dueDateServices);
        accountingServiceImpl.setDueDateHistoryRepository(dueDateHistoryRepository);
        
        accountingService = accountingServiceImpl;
    }

    @Test
    void testDoAccounting_SuccessWithCommission() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));

        EnumPaymentMethod mockPaymentMethodType = EnumPaymentMethod.CREDIT_CARD;  // Or any valid enum value
        createAccountingDTO.setPaymentMethodType(mockPaymentMethodType);

        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(2));
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
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
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));

        EnumPaymentMethod mockPaymentMethodType = EnumPaymentMethod.CREDIT_CARD;  // Or any valid enum value
        createAccountingDTO.setPaymentMethodType(mockPaymentMethodType);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
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
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();

        EnumPaymentMethod mockPaymentMethodType = EnumPaymentMethod.CREDIT_CARD;  // Or any valid enum value
        createAccountingDTO.setPaymentMethodType(mockPaymentMethodType);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(false);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        verify(receiptService, times(0)).printReceipt(any(), any());
    }

    @Test
    void testDoAccounting_NullPaymentMethodType() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        createAccountingDTO.setPaymentMethodType(null);  // Set PaymentMethodType as null

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        verify(provisionFactory, times(0)).getProvisionService(any());
        verify(provisionService, times(0)).doAccounting(any());
        verify(receiptService, times(0)).printReceipt(any(), any());
    }

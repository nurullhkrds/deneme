class AccountingServiceTest {

    @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReceiptService receiptService;

    @InjectMocks
    private AccountingService accountingService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testDoAccounting_SuccessWithCommission() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
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
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(false);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        verify(receiptService, times(0)).printReceipt(any(), any());
    }
}

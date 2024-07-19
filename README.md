 @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReceiptService receiptService;

    @InjectMocks
    private AccountingService accountingService; // Bu sınıfın adını değiştirin, orijinal sınıf ismi ile aynı olmalı

    @BeforeEach
    public void setUp() {
        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO(true));
    }

    @Test
    public void testDoAccounting_SuccessfulAccounting() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(2));
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(12), result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(112), result.getTotalPaymentAmount());

        verify(receiptService, times(1)).printReceipt(createAccountingDTO, result);
    }

    @Test
    public void testDoAccounting_NoCommission() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.ZERO, result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(100), result.getTotalPaymentAmount());

        verify(receiptService, times(1)).printReceipt(createAccountingDTO, result);
    }

    private CreateAccountingResultDTO createAccountingResultDTO(boolean isSuccess) {
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(isSuccess);
        createAccountingResultDTO.setPendingDetailList(Collections.emptyList());
        return createAccountingResultDTO;
    }

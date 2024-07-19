  @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this); // Mockito nesnelerini başlatır
        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO(true));
    }

    @Test
    public void testDoAccounting_SuccessfulAccounting() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CREDIT_CARD); // EnumPaymentMethod'dan bir örnek kullanın
        
        // ProvisionDTO ve CommissionData ayarlamaları
        ProvisionDTO provisionDTO = new ProvisionDTO();
        CommissionData commissionData = new CommissionData();
        commissionData.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        commissionData.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(2));
        provisionDTO.setCommissionData(commissionData);
        
        createAccountingDTO.setProvisionDTO(provisionDTO);

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
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CREDIT_CARD); // EnumPaymentMethod'dan bir örnek kullanın
        
        // ProvisionDTO ayarları
        createAccountingDTO.setProvisionDTO(null); // ProvisionDTO'nun null olduğu senaryo

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

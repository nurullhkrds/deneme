 @InjectMocks
    private AccountingService accountingService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingDateUtil accountingDateUtil;

    @Mock
    private CreateAccountingDTO createAccountingDTO;

    @Mock
    private CreateAccountingResultDTO createAccountingResultDTO;

    @Mock
    private MakeProvisionRequest makeProvisionRequest;

    @Mock
    private MakeProvisionResponse makeProvisionResponse;

    @Mock
    private AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testDoAccounting_Success() {
        // Arrange
        when(createAccountingDTO.getChannelTransactionId()).thenReturn("transactionId");
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode()).thenReturn("templateCode");
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
        when(createAccountingDTO.getPaymentAmount()).thenReturn(BigDecimal.valueOf(100));
        when(createAccountingDTO.getCurrency()).thenReturn(Currency.TRY); // Use your actual currency enum
        when(createAccountingDTO.getProvisionDTO().getCustomerNo()).thenReturn(123L);
        when(createAccountingDTO.getInstitution().getCustomerNo()).thenReturn(456L);
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode()).thenReturn(EnumBlockDayStrategyCode.NO_VALOR);
        when(accountingDateUtil.getAvailDate(any(), any())).thenReturn(LocalDate.now());

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class))).thenReturn(makeProvisionResponse);
        when(makeProvisionResponse.isSuccess()).thenReturn(true);
        when(makeProvisionResponse.getContractNo()).thenReturn("contractNo");
        when(makeProvisionResponse.getPendingDetailList()).thenReturn(Collections.emptyList());

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        assertTrue(result.isSuccess());
        assertEquals("contractNo", result.getContractNo());
    }

    @Test
    public void testDoAccounting_Failure_MakeProvisionResponseNotSuccess() {
        // Arrange
        when(createAccountingDTO.getChannelTransactionId()).thenReturn("transactionId");
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode()).thenReturn("templateCode");
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
        when(createAccountingDTO.getPaymentAmount()).thenReturn(BigDecimal.valueOf(100));
        when(createAccountingDTO.getCurrency()).thenReturn(Currency.TRY);
        when(createAccountingDTO.getProvisionDTO().getCustomerNo()).thenReturn(123L);
        when(createAccountingDTO.getInstitution().getCustomerNo()).thenReturn(456L);
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode()).thenReturn(EnumBlockDayStrategyCode.NO_VALOR);
        when(accountingDateUtil.getAvailDate(any(), any())).thenReturn(LocalDate.now());

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class))).thenReturn(makeProvisionResponse);
        when(makeProvisionResponse.isSuccess()).thenReturn(false);
        when(makeProvisionResponse.getErrorCode()).thenReturn(1001L);

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    @Test
    public void testDoAccounting_Failure_ExceptionHandling() {
        // Arrange
        when(createAccountingDTO.getChannelTransactionId()).thenReturn("transactionId");
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode()).thenReturn("templateCode");
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
        when(createAccountingDTO.getPaymentAmount()).thenReturn(BigDecimal.valueOf(100));
        when(createAccountingDTO.getCurrency()).thenReturn(Currency.TRY);
        when(createAccountingDTO.getProvisionDTO().getCustomerNo()).thenReturn(123L);
        when(createAccountingDTO.getInstitution().getCustomerNo()).thenReturn(456L);
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode()).thenReturn(EnumBlockDayStrategyCode.NO_VALOR);
        when(accountingDateUtil.getAvailDate(any(), any())).thenReturn(LocalDate.now());

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class))).thenThrow(new RuntimeException(new ServiceCallException(1002L)));

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

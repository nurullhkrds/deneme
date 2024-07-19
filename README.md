
    @InjectMocks
    private AccountingServiceImpl accountingService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingUtil accountingDateUtil;

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

    @Mock
    private InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO; // Add this mock

    @Mock
    private ProvisionDTO provisionDTO; // Add this mock

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);

        // Set up the mock objects to avoid NullPointerException
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO()).thenReturn(institutionChannelPymMethodDTO);
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode()).thenReturn("templateCode");
        when(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode()).thenReturn(EnumBlockDayStrategyCode.NO_VALOR);
        when(createAccountingDTO.getPaymentAmount()).thenReturn(BigDecimal.valueOf(100));
        when(createAccountingDTO.getCurrency()).thenReturn(EnumCurrencyCode.DOLAR);
        when(createAccountingDTO.getProvisionDTO()).thenReturn(provisionDTO);
        when(createAccountingDTO.getProvisionDTO().getCustomerNo()).thenReturn(123L);
        when(createAccountingDTO.getInstitution().getCustomerNo()).thenReturn(456L);
    }

    @Test
    public void testDoAccounting_Success() {
        // Arrange
        when(createAccountingDTO.getChannelTransactionId()).thenReturn("transactionId");
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
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
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
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
        when(createAccountingDTO.getChannelCode()).thenReturn("channelCode");
        when(createAccountingDTO.getAgentCode()).thenReturn("agentCode");
        when(createAccountingDTO.getBranchCode()).thenReturn("branchCode");
        when(accountingDateUtil.getAvailDate(any(), any())).thenReturn(LocalDate.now());

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class))).thenThrow(new RuntimeException(new ServiceCallException(1002L)));

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

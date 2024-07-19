public class AccountingProvisionServiceImplTest {

    @InjectMocks
    private AccountProvisionServiceImpl accountingService;

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
    private InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO;

    @Mock
    private ProvisionDTO provisionDTO;

    @Mock
    private InstitutionDTO institutionDTO;

    @Mock
    private InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO;

    @Mock
    private ResponseCommissionInformation responseCommissionInformation;

    @Mock
    private CommissionOutputAccountingApiDTO commissionOutputDTO;

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
        when(createAccountingDTO.getInstitution()).thenReturn(institutionDTO);
        when(institutionDTO.getCustomerNo()).thenReturn(456L);

        // Initialize the accountPaymentMethodDetailDTO mock
        when(createAccountingDTO.getPaymentMethodDetailDTO()).thenReturn(accountPaymentMethodDetailDTO);
        when(accountPaymentMethodDetailDTO.getAccountNo()).thenReturn("accountNo");

        // Initialize the institutionChnnlPymMthdAccDTO mock
        when(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO()).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccDTO.getInstitutionAccountNo()).thenReturn("institutionAccountNo");

        // Initialize the responseCommissionInformation mock
        when(createAccountingDTO.getResponseCommissionInformation()).thenReturn(responseCommissionInformation);
        when(responseCommissionInformation.getCommissionOutputAccountingApiDTOList())
            .thenReturn(Collections.singletonList(commissionOutputDTO));

        // Set up commissionOutputDTO mock
        when(commissionOutputDTO.getAmount()).thenReturn(BigDecimal.valueOf(50));
        when(commissionOutputDTO.getCurrency()).thenReturn(EnumCurrencyCode.DOLAR);
        when(commissionOutputDTO.getDescription()).thenReturn("Description");
        when(commissionOutputDTO.getIsCommissionTax()).thenReturn(false);
        when(commissionOutputDTO.getProvisionCode()).thenReturn("ProvisionCode");
        when(commissionOutputDTO.getReservationId()).thenReturn("ReservationId");
        when(commissionOutputDTO.getBalanceControlType()).thenReturn(EnumBalanceControlType.SOME_TYPE);
        when(commissionOutputDTO.getFeeDefinitionId()).thenReturn("FeeDefinitionId");
        when(commissionOutputDTO.getIsDelinquencyRequired()).thenReturn(false);
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
        when(makeProvisionResponse.getContractNo()).thenReturn(123L);
        when(makeProvisionResponse.getPendingDetailList()).thenReturn(Collections.emptyList());

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        assertTrue(result.isSuccess());
        assertEquals(123L, result.getContractNo());
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
}

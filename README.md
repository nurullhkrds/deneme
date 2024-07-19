public class CardProvisionServiceImplTest {

    @InjectMocks
    private CardProvisionServiceImpl cardProvisionServiceImpl;

    @Mock
    private SwtSwitchIntegrationService cardProvisionService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingUtilServiceImpl accountingUtilServiceImpl;

    @Mock
    private AccountingUtil accountingDateUtil;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testDoAccounting_Success() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setDummyMerchant(false);

        CreateAccountingResultDTO cardProvisionResultDTO = new CreateAccountingResultDTO();
        cardProvisionResultDTO.setSuccess(true);
        cardProvisionResultDTO.setOceanTransactionId(123456L);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(new CardProvisionResponse("123456"));

        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(789L);

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        // Assert
        assertTrue(resultDTO.isSuccess());
        assertEquals(789L, resultDTO.getContractNo());
    }

    @Test
    void testDoAccounting_Failure_ProvisionError() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setDummyMerchant(false);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision error"));

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, resultDTO.getError());
    }

    @Test
    void testDoAccounting_Failure_DummyMerchant() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setDummyMerchant(true);

        CreateAccountingResultDTO cardProvisionResultDTO = new CreateAccountingResultDTO();
        cardProvisionResultDTO.setSuccess(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(cardProvisionResultDTO);

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision error"));

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }

    @Test
    void testDoCardProvision_Success() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setChannelCode("123");
        createAccountingDTO.setDummyMerchant(false);

        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse("123456");
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(cardProvisionResponse);

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doCardProvision(createAccountingDTO);

        // Assert
        assertTrue(resultDTO.isSuccess());
        assertEquals(123456L, resultDTO.getOceanTransactionId());
    }

    @Test
    void testDoCardProvision_Failure() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setChannelCode("123");
        createAccountingDTO.setDummyMerchant(false);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision error"));

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doCardProvision(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, resultDTO.getError());
    }

    @Test
    void testDoGLAccounting_Success() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(true);
        makeProvisionResponse.setContractNo(789L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenReturn(makeProvisionResponse);

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doGLAccounting(createAccountingDTO, createAccountingResultDTO);

        // Assert
        assertTrue(resultDTO.isSuccess());
        assertEquals(789L, resultDTO.getContractNo());
    }

    @Test
    void testDoGLAccounting_Failure() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision error"));

        // Act
        CreateAccountingResultDTO resultDTO = cardProvisionServiceImpl.doGLAccounting(createAccountingDTO, createAccountingResultDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }

    @Test
    void testPrepareCardProvisionRequest() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setChannelCode("123");
        createAccountingDTO.setBranchCode("456");
        createAccountingDTO.setMerchantNo("789");
        createAccountingDTO.setPaymentAmount(BigDecimal.TEN);
        createAccountingDTO.setCurrency(CurrencyType.TRY);

        // Act
        CardProvisionRequest request = cardProvisionServiceImpl.prepareCardProvisionRequest(createAccountingDTO, "requestId");

        // Assert
        assertNotNull(request);
        assertEquals("requestId", request.getRequestId());
        assertEquals("123", request.getChannelCode());
        assertEquals("456", request.getBranchCode());
        assertEquals("789", request.getMerchantCode());
        assertEquals(BigDecimal.TEN, request.getAmount());
        assertEquals("TRY", request.getCurrencyCode());
    }

    @Test
    void testHandleException_UnknownError() {
        // Arrange
        Long errorCode = 999L; // Unknown error code
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        // Act
        cardProvisionServiceImpl.handleException(errorCode, createAccountingResultDTO);

        // Assert
        assertFalse(createAccountingResultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, createAccountingResultDTO.getError());
    }

    @Test
    void testGetBlockDayCount_DailyStrategy() {
        // Arrange
        InstitutionChannelPymMethodDTO pymMethodDTO = new InstitutionChannelPymMethodDTO();
        pymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        InstitutionChnnlPymMthdPscDTO pymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        pymMthdPscDTO.setBlockDayCount(3); // Assume Sunday

        when(Calendar.getInstance().get(Calendar.DAY_OF_WEEK)).thenReturn(Calendar.SUNDAY);

        // Act
        Integer blockDayCount = cardProvisionServiceImpl.getBlockDayCount(pymMethodDTO, pymMthdPscDTO);

        // Assert
        assertEquals(3, blockDayCount);
    }

    @Test
    void testGetBlockDayCount_ChannelStrategy() {
        // Arrange
        InstitutionChannelPymMethodDTO pymMethodDTO = new InstitutionChannelPymMethodDTO();
        pymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
        pymMethodDTO.setBlockDayCount(5);

        // Act
        Integer blockDayCount = cardProvisionServiceImpl.getBlockDayCount(pymMethodDTO, null);

        // Assert
        assertEquals(5, blockDayCount);
    }

    @Test
    void testGetBlockDayCount_NoValorStrategy() {
        // Arrange
        InstitutionChannelPymMethodDTO pymMethodDTO = new InstitutionChannelPymMethodDTO();
        pymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);

        // Act
        Integer blockDayCount = cardProvisionServiceImpl.getBlockDayCount(pymMethodDTO, null);

        // Assert
        assertEquals(0, blockDayCount);
    }
}

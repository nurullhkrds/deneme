@ExtendWith(MockitoExtension.class)
public class CardProvisionServiceImplTest {

    @Mock
    private SwtSwitchIntegrationService cardProvisionService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingUtilServiceImpl accountingUtilServiceImpl;

    @Mock
    private AccountingUtil accountingDateUtil;

    @InjectMocks
    private CardProvisionServiceImpl cardProvisionServiceImpl;

    private CreateAccountingDTO createAccountingDTO;

    @BeforeEach
    void setUp() {
        createAccountingDTO = new CreateAccountingDTO();
        
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setName("Test Institution");
        ProductDTO productDTO = new ProductDTO();
        productDTO.setProductCampaignCode("ProductCampaignCode");
        institutionDTO.setProduct(productDTO);
        institutionDTO.setId(1L);
        institutionDTO.setCustomerNo(1001L);
        createAccountingDTO.setInstitution(institutionDTO);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setAccountingTemplateCode("TemplateCode");
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
        createAccountingDTO.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);

        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("AccountNo");
        createAccountingDTO.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);

        createAccountingDTO.setCurrency(EnumCurrencyCode.USD);
        createAccountingDTO.setChannelCode("TestChannel");
        createAccountingDTO.setBranchCode("TestBranch");
        createAccountingDTO.setChannelSessionId("TestSessionId");
        createAccountingDTO.setMerchantNo("TestMerchantNo");
        createAccountingDTO.setPaymentAmount(BigDecimal.TEN);
        createAccountingDTO.setDummyMerchant(false);

        CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = new CreditCardPaymentMethodDetailDTO();
        creditCardPaymentMethodDetailDTO.setCardNumber("4111111111111111");
        createAccountingDTO.setPaymentMethodDetailDTO(creditCardPaymentMethodDetailDTO);

        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setCustomerNo(1001L);
        createAccountingDTO.setProvisionDTO(provisionDTO);

        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setInquiryId("InquiryId");
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.ONE);
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.ONE);
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        createAccountingDTO.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);
    }

    @Test
    void testDoAccounting_SuccessfulDummyMerchant() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertNotNull(result.getOceanTransactionId());
    }

    @Test
    void testDoAccounting_SuccessfulRealMerchant() {
        createAccountingDTO.setDummyMerchant(false);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));
        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(123456L);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(123456L, result.getContractNo());
    }

    @Test
    void testDoAccounting_CardProvisionFailure() {
        createAccountingDTO.setDummyMerchant(true);
        doThrow(new RuntimeException("Provision Failed")).when(cardProvisionService).doProvision(any(CardProvisionRequest.class));

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertFalse(result.isSuccess());
            assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception");
            }
        }
    }

    @Test
    void testDoAccounting_GLAccountingFailure() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));

        doThrow(new RuntimeException("GL Accounting Failed")).when(provisionNextService).makeProvision(any(MakeProvisionRequest.class));

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertFalse(result.isSuccess());
            assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception");
            }
        }
    }

    @Test
    void testDoAccounting_BlockDayCount() {
        createAccountingDTO.setDummyMerchant(false);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = createAccountingDTO.getInstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
        when(institutionChannelPymMethodDTO.getBlockDayCount()).thenReturn(10);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));
        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(123456L);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        verify(accountingDateUtil, times(1))
                .getAvailDate(any(EnumBlockDayType.class), anyInt());
    }

    @Test
    void testDoCardProvision_Success() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doCardProvision(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals("guid123", result.getOceanTransactionId().toString());
    }

    @Test
    void testDoCardProvision_Failure() {
        createAccountingDTO.setDummyMerchant(true);
        doThrow(new RuntimeException("Provision Failed")).when(cardProvisionService).doProvision(any(CardProvisionRequest.class));

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doCardProvision(createAccountingDTO);
            assertFalse(result.isSuccess());
            assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception");
            }
        }
    }

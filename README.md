class CardProvisionServiceImplTest {

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

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetProvisionType() {
        assertEquals(EnumProvisionType.CARD, cardProvisionServiceImpl.getProvisionType());
    }

    @Test
    void whenCreateAccountingResultDTOisFailed() throws BusinessException, ServiceCallException {
        // Test verilerini oluştur
        ProvisionDTO dto1 = new ProvisionDTO();
        dto1.setId(1L);
        dto1.setStatus(EnumProvisionStatus.PAID);

        ResponseCommissionInformation dto2 = new ResponseCommissionInformation();
        dto2.setInquiryId("123");
        dto2.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(10)); // Null olmamasını sağla
        dto2.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(5)); // Null olmamasını sağla
        ProductDTO dto10=new ProductDTO();
        dto10.setName("product");
        dto10.setCode("123");



        InstitutionDTO dto3 = new InstitutionDTO();
        dto3.setId(1L);
        dto3.setProduct(dto10);
        dto3.setCustomerNo(123L);

        InstitutionDebtTypeDTO dto4 = new InstitutionDebtTypeDTO();
        dto4.setId(123L);

        InstitutionChannelPymMethodDTO dto5 = new InstitutionChannelPymMethodDTO();
        dto5.setId(123L);
        dto5.setIsActive(true);

        InstitutionChnnlPymMthdAccDTO dto6 = new InstitutionChnnlPymMthdAccDTO();
        dto6.setId(123L);
        dto6.setIsActive(true);

        InstitutionChnnlPymMthdPscDTO dto7 = new InstitutionChnnlPymMthdPscDTO();
        dto7.setId(123L);

        InstitutionAccountingInfoDTO dto8 = new InstitutionAccountingInfoDTO();
        dto8.setId(123L);

        CreateAccountingDTO inputDto = new CreateAccountingDTO();
        inputDto.setCurrency(EnumCurrencyCode.DOLAR);
        inputDto.setProvisionDTO(dto1);
        inputDto.setResponseCommissionInformation(dto2);
        inputDto.setInstitution(dto3);
        inputDto.setInstitutionDebtType(dto4);
        inputDto.setInstitutionChannelPymMethodDTO(dto5);
        inputDto.setInstitutionChnnlPymMthdAccDTO(dto6);
        inputDto.setInstitutionChnnlPymMthdPscDTO(dto7);
        inputDto.setInstitutionAccountingInfoDTO(dto8);
        inputDto.setChannelCode("123");
        inputDto.setDummyMerchant(true);
        inputDto.setPaymentAmount(BigDecimal.valueOf(100)); // PaymentAmount alanını ayarla

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        String provisionRequestId = "123";

        CardProvisionRequest cardProvisionRequest = new CardProvisionRequest();
        cardProvisionRequest.setRequestId("123");
        cardProvisionRequest.setRequestDate(DateUtils.formatLocalDateTime(LocalDateTime.now(), DateUtils.DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS));
        cardProvisionRequest.setSessionId(inputDto.getChannelSessionId());
        cardProvisionRequest.setChannelCode(inputDto.getChannelCode());
        cardProvisionRequest.setTransactionType(BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_DUMMY);

        CardProvisionCommissionInfoDTO commissionInfoDTO = new CardProvisionCommissionInfoDTO();
        ResponseCommissionInformation responseCommissionInformation = inputDto.getResponseCommissionInformation();
        if (responseCommissionInformation != null) {
            commissionInfoDTO.setReferenceCode(responseCommissionInformation.getInquiryId());
            commissionInfoDTO.setAmount(responseCommissionInformation.getTotalCommissionTaxLocalCurrencyAmount().add(responseCommissionInformation.getTotalCommissionLocalCurrencyAmount()));
            commissionInfoDTO.setCurrencyCode(CommonUtils.currencyConverter(inputDto.getCurrency().getValue())); // TODO: Currency kontrol edilecek
        }
        cardProvisionRequest.setCommissionInfo(commissionInfoDTO);
        cardProvisionRequest.setAmount(inputDto.getPaymentAmount().add(commissionInfoDTO.getAmount()));

        CardProvisionInstallmentInfoDTO installmentInfoDTO = new CardProvisionInstallmentInfoDTO();
        installmentInfoDTO.setInstallmentAmount(BigDecimal.ZERO);
        installmentInfoDTO.setInstallmentCount(0);
        installmentInfoDTO.setInterestRate(BigDecimal.ZERO);
        installmentInfoDTO.setInterestAmount(BigDecimal.ZERO);
        cardProvisionRequest.setInstallmentInfo(installmentInfoDTO);

        List<KeyValueDto> additionalTransactionInfoList = new ArrayList<>();

        KeyValueDto additionalProductCodeInfo = new KeyValueDto();
        additionalProductCodeInfo.setKey("productCode");
        additionalProductCodeInfo.setValue(inputDto.getInstitution().getProduct().getProductCampaignCode());
        additionalTransactionInfoList.add(additionalProductCodeInfo);

        KeyValueDto additionalInstitutionCodeInfo = new KeyValueDto();
        additionalInstitutionCodeInfo.setKey("institutionCode");
        additionalInstitutionCodeInfo.setValue(inputDto.getInstitution().getId().toString());
        additionalTransactionInfoList.add(additionalInstitutionCodeInfo);

        cardProvisionRequest.setAdditionalTransactionInfoList(additionalTransactionInfoList);

        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
        cardProvisionResponse.setGuid("123");
        cardProvisionResponse.setRequestId("123");

        createAccountingResultDTO.setOceanTransactionId(Long.parseLong(cardProvisionResponse.getGuid())); // TODO: Doğru mu teyit edilecek
        createAccountingResultDTO.setProvisionRequestId(provisionRequestId);
        createAccountingResultDTO.setSuccess(true);

        Mockito.when(cardProvisionService.doProvision(cardProvisionRequest)).thenReturn(cardProvisionResponse);
        Mockito.when(CommonUtils.generateCreditCardProvisionRequestId(inputDto.getChannelCode(), inputDto.isDummyMerchant())).thenReturn(provisionRequestId);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(inputDto);

        assertEquals(createAccountingResultDTO.isSuccess(), result.isSuccess());
    }





}

@Test
void testDoAccountingSuccessRealMerchant() throws BusinessException, ServiceCallException {
    CreateAccountingDTO dto = new CreateAccountingDTO();
    dto.setDummyMerchant(false);
    dto.setPaymentAmount(BigDecimal.valueOf(100));
    dto.setCurrency(EnumCurrencyCode.DOLAR);
    dto.setChannelCode("channelCode");
    dto.setChannelSessionId("sessionId");
    dto.setBranchCode("branchCode");
    dto.setMerchantNo("merchantNo");

    ProvisionDTO provisionDTO = new ProvisionDTO();
    provisionDTO.setId(1L);
    dto.setProvisionDTO(provisionDTO);

    InstitutionDTO institutionDTO = new InstitutionDTO();
    institutionDTO.setId(1L);
    institutionDTO.setName("Institution");
    institutionDTO.setProduct(new ProductDTO());
    dto.setInstitution(institutionDTO);

    InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
    institutionChannelPymMethodDTO.setAccountingTemplateCode("templateCode");
    institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
    dto.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);

    InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
    institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("accountNo");
    dto.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);

    InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
    institutionChnnlPymMthdPscDTO.setId(1L);
    dto.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);

    CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = new CreditCardPaymentMethodDetailDTO();
    creditCardPaymentMethodDetailDTO.setCardNumber("1234567812345678"); // Örnek kart numarası
    dto.setPaymentMethodDetailDTO(creditCardPaymentMethodDetailDTO);

    CreateAccountingResultDTO cardProvisionResultDTO = new CreateAccountingResultDTO();
    cardProvisionResultDTO.setSuccess(true);
    cardProvisionResultDTO.setProvisionRequestId("requestId");

    // Mock ayarları
    when(accountingUtilServiceImpl.getContractNumber()).thenReturn(12345L);

    // `CardProvisionResponse` nesnesini `guid` ile döndür
    CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
    cardProvisionResponse.setGuid("guid");
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class))).thenReturn(cardProvisionResponse);

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);

    // Sonuçları doğrula
    assertTrue(result.isSuccess(), "Expected success to be true");
    assertEquals(12345L, result.getContractNo(), "Expected contract number to be 12345");
    assertEquals("guid", result.getProvisionRequestId(), "Expected provision request ID to be 'guid'");
}

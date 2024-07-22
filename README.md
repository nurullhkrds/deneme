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
        ProvisionDTO provisionDTO=new ProvisionDTO();
        provisionDTO.setId(1L);
        InstitutionDTO institutionDTO=new InstitutionDTO();
        institutionDTO.setId(1L);
        institutionDTO.setName("Institution");
        institutionDTO.setProduct(new ProductDTO());
        dto.setInstitution(institutionDTO);
        dto.setProvisionDTO(provisionDTO);
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO=new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setAccountingTemplateCode("templateCode");
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
        dto.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO=new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("accountNo");
        dto.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO=new InstitutionChnnlPymMthdPscDTO();
        institutionChnnlPymMthdPscDTO.setId(1L);
        dto.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);

        CreateAccountingResultDTO cardProvisionResultDTO = new CreateAccountingResultDTO();
        cardProvisionResultDTO.setSuccess(true);
        cardProvisionResultDTO.setProvisionRequestId("requestId");

        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(12345L);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse());

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertTrue(result.isSuccess());
        assertEquals(12345L, result.getContractNo());
        assertEquals("requestId", result.getProvisionRequestId());
    }

java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.payment.dto.CreditCardPaymentMethodDetailDTO.getCardNumber()" because "creditCardPaymentMethodDetailDTO" is null

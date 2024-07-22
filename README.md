 @Test
    void whenCreateAccountingResultDTOisFailed() throws BusinessException, ServiceCallException {

        ProvisionDTO dto1=new ProvisionDTO();
        dto1.setId(1L);
        dto1.setStatus(EnumProvisionStatus.PAID);
        ResponseCommissionInformation dto2=new ResponseCommissionInformation();
        dto2.setInquiryId("123");
        InstitutionDTO dto3=new InstitutionDTO();
        dto3.setId(1L);
        dto3.setCustomerNo(123L);
        InstitutionDebtTypeDTO dto4=new InstitutionDebtTypeDTO();
        dto4.setId(123L);
        InstitutionChannelPymMethodDTO dto5=new InstitutionChannelPymMethodDTO();
        dto5.setId(123L);
        dto5.setIsActive(true);
        InstitutionChnnlPymMthdAccDTO dto6=new InstitutionChnnlPymMthdAccDTO();
        dto6.setId(123L);
        dto6.setIsActive(true);

        InstitutionChnnlPymMthdPscDTO dto7= new InstitutionChnnlPymMthdPscDTO();
        dto7.setId(123L);
        InstitutionAccountingInfoDTO dto8=new InstitutionAccountingInfoDTO();
        dto8.setId(123L);


        CreateAccountingDTO inputDto=new CreateAccountingDTO();
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
        //dummymerchant true ise
        inputDto.setDummyMerchant(true);
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        String provisionRequestId = "123";

        CardProvisionRequest cardProvisionRequest=new CardProvisionRequest();
        cardProvisionRequest.setRequestId("123");

        cardProvisionRequest.setRequestId(provisionRequestId);
        cardProvisionRequest.setRequestDate(DateUtils.formatLocalDateTime(LocalDateTime.now(), DateUtils.DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS));
        cardProvisionRequest.setSessionId(inputDto.getChannelSessionId());
        cardProvisionRequest.setChannelCode(inputDto.getChannelCode());
        cardProvisionRequest.setTransactionType(BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_DUMMY);


        CardProvisionCardInfoDTO cardInfoDTO = new CardProvisionCardInfoDTO();
        cardInfoDTO.setEncKeyIndex((short) 0);
        cardInfoDTO.setEncrypted(Boolean.FALSE);
        CardProvisionCommissionInfoDTO commissionInfoDTO = new CardProvisionCommissionInfoDTO();
        //getResponseCommissionInformation null değilse
        ResponseCommissionInformation responseCommissionInformation = inputDto.getResponseCommissionInformation() ;
        commissionInfoDTO.setReferenceCode(responseCommissionInformation.getInquiryId());
        commissionInfoDTO.setAmount(responseCommissionInformation.getTotalCommissionTaxLocalCurrencyAmount().add(responseCommissionInformation.getTotalCommissionLocalCurrencyAmount()));
        cardProvisionRequest.setAmount(inputDto.getPaymentAmount().add(commissionInfoDTO.getAmount()));
        commissionInfoDTO.setCurrencyCode(CommonUtils.currencyConverter(inputDto.getCurrency().getValue())); //TODO  ytodo currency kontrol edilecek
        cardProvisionRequest.setCommissionInfo(commissionInfoDTO);
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

        CardProvisionRequest cardProvisionRequest1=cardProvisionRequest;
        CardProvisionResponse cardProvisionResponse=new CardProvisionResponse();
        cardProvisionResponse.setGuid("guid");
        cardProvisionResponse.setRequestId("123");



        // guid boş değilse
        createAccountingResultDTO.setOceanTransactionId(Long.parseLong(cardProvisionResponse.getGuid())); //TODO ytodo eslestirme dogru mu teyit edilecek. servis string biz long tutmusuz tabloda teyit edilecek
        createAccountingResultDTO.setProvisionRequestId(provisionRequestId);
        createAccountingResultDTO.setSuccess(true);


        CreateAccountingResultDTO result= cardProvisionServiceImpl.doAccounting(inputDto);


        Mockito.when(cardProvisionService.doProvision(cardProvisionRequest1)).thenReturn(cardProvisionResponse);
        Mockito.when(CommonUtils.generateCreditCardProvisionRequestId(inputDto.getChannelCode(),inputDto.isDummyMerchant())).thenReturn(provisionRequestId);

        assertEquals(createAccountingResultDTO.isSuccess(),result.isSuccess());
    }
java.lang.NullPointerException: Cannot invoke "java.math.BigDecimal.add(java.math.BigDecimal)" because the return value of "com.ykb.payments.bill.transaction.external.corebanking.commission.model.response.ResponseCommissionInformation.getTotalCommissionTaxLocalCurrencyAmount()" is null

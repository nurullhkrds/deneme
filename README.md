 private CreateAccountingResultDTO doGLAccounting(CreateAccountingDTO createAccountingDTO,CreateAccountingResultDTO createAccountingResultDTO) {
        MakeProvisionRequest  makeProvisionRequest = prepareProvisionRequest(createAccountingDTO,createAccountingResultDTO);
        try {
            MakeProvisionResponse makeProvisionResponse = provisionNextService.makeProvision(makeProvisionRequest); //TODO: Exception mapping yapılacak
            if(!makeProvisionResponse.isSuccess()){
                handleException(makeProvisionResponse.getErrorCode(), createAccountingResultDTO);
                createAccountingResultDTO.setSuccess(false);
                return createAccountingResultDTO;
            }
            if(makeProvisionResponse.getContractNo() == null){
                createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
                createAccountingResultDTO.setSuccess(false);
                return createAccountingResultDTO;
            }
            createAccountingResultDTO.setContractNo(makeProvisionResponse.getContractNo());
            createAccountingResultDTO.setPendingDetailList(makeProvisionResponse.getPendingDetailList());
            createAccountingResultDTO.setSuccess(true);
        }catch (Exception e){
            if(e.getCause().getClass().equals(ServiceCallException.class)){
                Long errorCode =((ServiceCallException) e.getCause()).getErrorCode();
                handleException(errorCode, createAccountingResultDTO);
                return createAccountingResultDTO;
            }
            createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
            createAccountingResultDTO.setSuccess(false);
        }
        return createAccountingResultDTO;
    }


    private MakeProvisionRequest prepareProvisionRequest(CreateAccountingDTO createAccountingDTO,CreateAccountingResultDTO createAccountingResultDTO )  {
        MakeProvisionRequest makeProvisionRequest = new MakeProvisionRequest();
        makeProvisionRequest.setTransactionId(createAccountingDTO.getChannelTransactionId());
        makeProvisionRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        makeProvisionRequest.setChannelCode(createAccountingDTO.getChannelCode());
        makeProvisionRequest.setUserCode(createAccountingDTO.getAgentCode());
        makeProvisionRequest.setOperationalBranchCode(createAccountingDTO.getBranchCode());

        List<MakeProvisionInnerDTO> makeProvisionInnerList = new ArrayList<>();
        
        
        StringBuilder eventDescription = new StringBuilder();
        
        CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = (CreditCardPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();
        
        eventDescription.append(creditCardPaymentMethodDetailDTO.getCardNumber()).append("-");
		
		String creditCardCode = ChannelUtil.convertCardEventDescChannel(createAccountingDTO.getChannelCode());
		
		eventDescription.append(creditCardCode);
				
		eventDescription.append("-");
		
		SimpleDateFormat creditCardDateFormatter = new SimpleDateFormat(BillPaymentsConsts.DATE_FORMAT__CREDIT_CARD_ACCOUNTING_DATE_FORMAT);
		
		eventDescription.append(creditCardDateFormatter.format(Calendar.getInstance().getTime()));
		
		String description = eventDescription.toString();
		
		description = description.length() > 34 ? description.substring(0, 34) : description;
		
        /** Debit */
        MakeProvisionInnerDTO debitProvisionInnerRequest = new MakeProvisionInnerDTO();
        debitProvisionInnerRequest.setAccountNo(null);
        debitProvisionInnerRequest.setCurrency(createAccountingDTO.getCurrency().getValue());
        debitProvisionInnerRequest.setAmount(createAccountingDTO.getPaymentAmount().negate());
        debitProvisionInnerRequest.setDescription(description);
        debitProvisionInnerRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        debitProvisionInnerRequest.setClientNo(createAccountingDTO.getProvisionDTO().getCustomerNo().intValue());
        debitProvisionInnerRequest.setDelinquencyRequired(false);
        debitProvisionInnerRequest.setCommissionTax(false);
        debitProvisionInnerRequest.setCommission(false);
        debitProvisionInnerRequest.setGlRow(true);
        makeProvisionInnerList.add(debitProvisionInnerRequest);


        /** Credit */
        MakeProvisionInnerDTO creditProvisionInnerRequest = new MakeProvisionInnerDTO();
        creditProvisionInnerRequest.setAccountNo(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO().getInstitutionAccountNo());
        creditProvisionInnerRequest.setCurrency(createAccountingDTO.getCurrency().getValue());
        creditProvisionInnerRequest.setAmount(createAccountingDTO.getPaymentAmount());
        creditProvisionInnerRequest.setDescription(description.substring(0, 6) + "******" + description.substring(12));
        creditProvisionInnerRequest.setProvisionCode(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode());
        creditProvisionInnerRequest.setClientNo(createAccountingDTO.getInstitution().getCustomerNo().intValue());
        //TODO:LocalAmount dövizlerde

        LocalDate availableLocalDate ;
        if(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.NO_VALOR)){
            availableLocalDate = LocalDate.now();
        }else {
            availableLocalDate = accountingDateUtil.getAvailDate(createAccountingDTO.getInstitutionChannelPymMethodDTO().getBlockDayType(),
                    getBlockDayCount(createAccountingDTO.getInstitutionChannelPymMethodDTO(), createAccountingDTO.getInstitutionChnnlPymMthdPscDTO()));
        }
        createAccountingResultDTO.setAvailableDate(availableLocalDate);
        Date availableDate = Date.valueOf(availableLocalDate);
        Date valueDate = Date.valueOf(availableLocalDate.plusDays(1));
        creditProvisionInnerRequest.setAvailableDate(availableDate);
        creditProvisionInnerRequest.setValueDate(valueDate);

        makeProvisionInnerList.add(creditProvisionInnerRequest);

        makeProvisionRequest.setMakeProvisionInnerList(makeProvisionInnerList);


        return makeProvisionRequest;
    }
    //TODO: utill e al
    private void handleException(Long errorCode,CreateAccountingResultDTO createAccountingResultDTO){
        EnumAccountProvisionResult result = EnumAccountProvisionResult.parse(errorCode);
        createAccountingResultDTO.setSuccess(false);
        if(result == null){
            createAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
            return;
        }
        createAccountingResultDTO.setError(result.getBillCode());
    }

    private Integer getBlockDayCount(InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO, InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO){
        if(institutionChannelPymMethodDTO.getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.DAILY)){
            return  institutionChnnlPymMthdPscDTO.getBlockDayCount(Calendar.getInstance().get(Calendar.DAY_OF_WEEK));
        }else if(institutionChannelPymMethodDTO.getBlockDayStrategyCode().equals(EnumBlockDayStrategyCode.CHANNEL)){
            return  institutionChannelPymMethodDTO.getBlockDayCount();
        }else{
            return 0;
        }
    }

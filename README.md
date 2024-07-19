    @Override
    public CreateAccountingResultDTO doAccounting(CreateAccountingDTO createAccountingDTO) {
        ProvisionService provisionService = provisionFactory.getProvisionService(createAccountingDTO.getPaymentMethodType().getProvisionType());
        prepareCreateAccountingDTO(createAccountingDTO);
        CreateAccountingResultDTO createAccountingResultDTO = provisionService.doAccounting(createAccountingDTO);
        if(createAccountingResultDTO.isSuccess()) {
            prepareCreateAccountingResultDTO(createAccountingResultDTO,createAccountingDTO);
            receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);
        }
        return createAccountingResultDTO;
    }


 private CreateAccountingResultDTO prepareCreateAccountingResultDTO(CreateAccountingResultDTO createAccountingResultDTO, CreateAccountingDTO createAccountingDTO) {

        BigDecimal totalPaymentAmount = createAccountingDTO.getPaymentAmount();
        if (createAccountingDTO.getResponseCommissionInformation() != null) {
            BigDecimal commissionAmount = createAccountingDTO.getResponseCommissionInformation().getTotalCommissionLocalCurrencyAmount();
            commissionAmount = commissionAmount.add(createAccountingDTO.getResponseCommissionInformation().getTotalCommissionTaxLocalCurrencyAmount());
            createAccountingResultDTO.setCommissionAmount(commissionAmount);
            totalPaymentAmount = totalPaymentAmount.add(commissionAmount);
        } else {
            createAccountingResultDTO.setCommissionAmount(BigDecimal.ZERO);
        }

        createAccountingResultDTO.setTotalPaymentAmount(totalPaymentAmount);
        createAccountingResultDTO.setPendingDetailList(createAccountingResultDTO.getPendingDetailList());
        return createAccountingResultDTO;
    }

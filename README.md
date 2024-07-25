@Service
@RequiredArgsConstructor
public class ReceiptServiceImpl implements ReceiptService{

    private final ReceiptApiService receiptService;


    @Override
    public void printReceipt(CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO )  {

        List<RequestApiReceiptDTO> printReceiptRequest = new ArrayList<>();

        RequestApiReceiptDTO debitRequestApiReceiptDTO = prepareDebitRequestApiReceiptDTO(createAccountingDTO, createAccountingResultDTO);
        printReceiptRequest.add(debitRequestApiReceiptDTO);

        if(!(createAccountingDTO.getPaymentMethodType().getProvisionType().equals(EnumProvisionType.CARD) && !createAccountingDTO.isDummyMerchant())){
            RequestApiReceiptDTO creditRequestApiReceiptDTO = prepareCreditRequestApiReceiptDTO(createAccountingDTO, createAccountingResultDTO);
            printReceiptRequest.add(creditRequestApiReceiptDTO);
        }

        receiptService.printReceipt(printReceiptRequest);

    }

    private RequestApiReceiptDTO prepareDebitRequestApiReceiptDTO(CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO)  {

        RequestApiReceiptDTO requestApiReceiptDTO = prepareReceiptDebitMaster(createAccountingDTO, createAccountingResultDTO);

        requestApiReceiptDTO.setReceiptDetailList(prepareReceiptDebitDetails(createAccountingDTO, createAccountingResultDTO));

        return requestApiReceiptDTO;
    }



    private RequestApiReceiptDTO prepareReceiptDebitMaster(CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO) {

        RequestApiReceiptDTO requestApiReceiptDTO = new RequestApiReceiptDTO();


        if (createAccountingDTO.getPaymentMethodType().getProvisionType().equals(EnumProvisionType.ACCOUNT)) {
            AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO = (AccountPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();
            requestApiReceiptDTO.setAccount(accountPaymentMethodDetailDTO.getAccountNo() + "    " + CommonUtils.currencyConverter(createAccountingDTO.getCurrency().getValue()));

        } else if (createAccountingDTO.getPaymentMethodType().getProvisionType().equals(EnumProvisionType.CARD)) {
            CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = (CreditCardPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();
            requestApiReceiptDTO.setCreditCardNo(creditCardPaymentMethodDetailDTO.getCardNumber());
        } else {
           //TODO:CASH
        }

        requestApiReceiptDTO.setBranchCode(createAccountingDTO.getBranchCode());
        requestApiReceiptDTO.setClientNo(createAccountingDTO.getProvisionDTO().getCustomerNo().intValue());
        requestApiReceiptDTO.setCtype(AccountingConstant.C_TYPE);
        requestApiReceiptDTO.setDtype(AccountingConstant.D_TYPE);
        requestApiReceiptDTO.setProcessCode(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode());

        requestApiReceiptDTO.setProcessDate(CollectionUtils.isEmpty(createAccountingResultDTO.getPendingDetailList()) ? DateUtils.getCurrentDate() : createAccountingResultDTO.getPendingDetailList().get(0).getAccountingDate());
        requestApiReceiptDTO.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        requestApiReceiptDTO.setValueDate(CollectionUtils.isEmpty(createAccountingResultDTO.getPendingDetailList()) ? LocalDate.now() : DateUtils.convertDateTOLocalDate(createAccountingResultDTO.getPendingDetailList().get(0).getAccountingDate()));

        requestApiReceiptDTO.setContratNo(createAccountingResultDTO.getContractNo());
        requestApiReceiptDTO.setAmount(createAccountingResultDTO.getTotalPaymentAmount().negate());
        requestApiReceiptDTO.setUserCode(createAccountingDTO.getAgentCode());
        requestApiReceiptDTO.setChannel(createAccountingDTO.getChannelCode());
        requestApiReceiptDTO.setDescription(prepareDescription(createAccountingDTO));
        requestApiReceiptDTO.setReverse("N");
        requestApiReceiptDTO.setInvoiceNoFlag(Boolean.FALSE) ;
        requestApiReceiptDTO.setProcessID(createAccountingDTO.getChannelTransactionId());
        requestApiReceiptDTO.setBasilacak("Y");
        requestApiReceiptDTO.setTransactionId(createAccountingDTO.getChannelTransactionId());
        if(CollectionUtils.isEmpty(createAccountingResultDTO.getPendingDetailList())){
            return requestApiReceiptDTO;
        }
        List<Long> pendingTransactionIdList = new ArrayList<>();
        for (ProvisionDetailDTO provisionDetailDTO : createAccountingResultDTO.getPendingDetailList()) {
            if (provisionDetailDTO.getAmount().compareTo(BigDecimal.ZERO)<0  && !provisionDetailDTO.isCommission()
            && !provisionDetailDTO.isCommissionTax()) {
                pendingTransactionIdList.add(provisionDetailDTO.getPendingTransactionDetailId());
                break;
            }
        }
        requestApiReceiptDTO.setPendingTransactionIdList(pendingTransactionIdList);

        return requestApiReceiptDTO;
    }


    private List<RequestReceiptDetailDTO> prepareReceiptDebitDetails(CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO) {

        List<RequestReceiptDetailDTO> receiptDetailList = new ArrayList<>();


        if(EnumReceiptType.valueOf(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode()).getEnumClass().equals(EnumPymBill.class))
        {
            for (EnumPymBill pymBill : EnumPymBill.values()) {
                prepareRequestReceiptDetailDTO(pymBill.getLblFieldOrder(), pymBill.getValueFieldOrder(), pymBill.getReceiptFieldType(),
                        pymBill.getSourceType(), createAccountingDTO, createAccountingResultDTO, receiptDetailList);
            }
        }

        return receiptDetailList;

    }

    private void prepareRequestReceiptDetailDTO(Integer lblFieldOrder, Integer valueFieldOrder, EnumReceiptFieldType receiptFieldType,
            EnumReceiptSourceType sourceType,CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO,  List<RequestReceiptDetailDTO> receiptDetailList){
        if (receiptFieldType.equals(EnumReceiptFieldType.ONLY_LABEL_FIELD) || receiptFieldType.equals(EnumReceiptFieldType.COMPLETE_FIELD)) {
            RequestReceiptDetailDTO requestReceiptDetailDTO = new RequestReceiptDetailDTO();
            requestReceiptDetailDTO.setContractNo(createAccountingResultDTO.getContractNo());
            requestReceiptDetailDTO.setProcessCode(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode());
            requestReceiptDetailDTO.setFieldOrder(lblFieldOrder);
            requestReceiptDetailDTO.setValue(sourceType.getCode());
            receiptDetailList.add(requestReceiptDetailDTO);
        }
        if (receiptFieldType.equals(EnumReceiptFieldType.ONLY_VALUE_FIELD) || receiptFieldType.equals(EnumReceiptFieldType.COMPLETE_FIELD)) {
            RequestReceiptDetailDTO requestReceiptDetailDTO = new RequestReceiptDetailDTO();
            requestReceiptDetailDTO.setContractNo(createAccountingResultDTO.getContractNo());
            requestReceiptDetailDTO.setProcessCode(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode());
            requestReceiptDetailDTO.setFieldOrder(valueFieldOrder);
            String fieldValue = prepareFieldValue(sourceType, createAccountingDTO, createAccountingResultDTO);
            requestReceiptDetailDTO.setValue(fieldValue);
            if(sourceType.isNumeric()){
                fieldValue = StringUtils.isEmpty(fieldValue) ? "0" : fieldValue;
                requestReceiptDetailDTO.setFieldType("N");
                requestReceiptDetailDTO.setAmttype("AMT");
                fieldValue  = fieldValue.replace(".","");
                requestReceiptDetailDTO.setNumericValue(new BigDecimal(fieldValue.replace(",",".")));
            }

            receiptDetailList.add(requestReceiptDetailDTO);
        }


    }

    private String prepareFieldValue(EnumReceiptSourceType sourceType,CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO){
        String fieldValue = null;
        if(sourceType.equals(EnumReceiptSourceType.BILL_AMOUNT)) {
            fieldValue = AccountingUtil.formatCurrency(createAccountingDTO.getPaymentAmount().negate().toString());
        }else if(sourceType.equals(EnumReceiptSourceType.BILL_DUE_DATE)){
            fieldValue = DateUtils.formatLocalDate(createAccountingDTO.getProvisionDTO().getBillDueDate(), DateUtils.DATE_FORMAT_DD_MM_YYYY_WITH_SLASH);
        } else if(sourceType.equals(EnumReceiptSourceType.BILL_NO)){
            fieldValue = createAccountingDTO.getProvisionDTO().getBillNo();
        }else if(sourceType.equals(EnumReceiptSourceType.BILL_TERM)){
            fieldValue = createAccountingDTO.getProvisionDTO().getBillTerm();
        }else if(sourceType.equals(EnumReceiptSourceType.COMMISSION_AMOUNT)){
            fieldValue = AccountingUtil.formatCurrency(createAccountingResultDTO.getCommissionAmount().negate().toString());
        }else if(sourceType.equals(EnumReceiptSourceType.CREDIT_PAYMENT_SOURCE)){
            if (createAccountingDTO.getPaymentMethodType().getProvisionType().equals(EnumProvisionType.ACCOUNT)) {
                AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO = (AccountPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();
                fieldValue = accountPaymentMethodDetailDTO.getAccountNo();
            } else if (createAccountingDTO.getPaymentMethodType().getProvisionType().equals(EnumProvisionType.CARD)) {
                CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = (CreditCardPaymentMethodDetailDTO) createAccountingDTO.getPaymentMethodDetailDTO();
                fieldValue = creditCardPaymentMethodDetailDTO.getCardNumber();
            }
        }else if(sourceType.equals(EnumReceiptSourceType.INSTITUTION_NAME)){
            fieldValue = createAccountingDTO.getInstitution().getName();
        }else if(sourceType.equals(EnumReceiptSourceType.CURRENCY)){
            fieldValue = CurrencyUtil.currencyConverter(createAccountingDTO.getCurrency().getValue());
        }else if(sourceType.equals(EnumReceiptSourceType.PAYMENT_AMOUNT)){
            fieldValue = AccountingUtil.formatCurrency(createAccountingDTO.getPaymentAmount().negate().toString());
        }else if(sourceType.equals(EnumReceiptSourceType.SUBSCRIBER_NO)){
            fieldValue = createAccountingDTO.getProvisionDTO().getSubscriberNo();
        }else if(sourceType.equals(EnumReceiptSourceType.TOTAL_AMOUNT)){
            fieldValue = AccountingUtil.formatCurrency(createAccountingResultDTO.getTotalPaymentAmount().negate().toString());
        }
        return fieldValue;
    }

    private String prepareDescription(CreateAccountingDTO createAccountingDTO) {
        return createAccountingDTO.getInstitution().getName() + "-" + createAccountingDTO.getProvisionDTO().getSubscriberNo();
    }

    private RequestApiReceiptDTO prepareCreditRequestApiReceiptDTO(CreateAccountingDTO createAccountingDTO, CreateAccountingResultDTO createAccountingResultDTO) {

        RequestApiReceiptDTO requestApiReceiptDTO = new RequestApiReceiptDTO();
        requestApiReceiptDTO.setTransactionId(createAccountingDTO.getChannelTransactionId());
        requestApiReceiptDTO.setAccount(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO().getInstitutionAccountNo() + "    " + CommonUtils.currencyConverter(createAccountingDTO.getCurrency().getValue()));
        requestApiReceiptDTO.setBranchCode(createAccountingDTO.getBranchCode());
        requestApiReceiptDTO.setClientNo(createAccountingDTO.getInstitution().getCustomerNo().intValue());
        requestApiReceiptDTO.setCtype(AccountingConstant.C_TYPE);
        requestApiReceiptDTO.setDtype(AccountingConstant.D_TYPE);
        requestApiReceiptDTO.setProcessCode(AccountingConstant.DKNTSABIT);
        requestApiReceiptDTO.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        requestApiReceiptDTO.setContratNo(createAccountingResultDTO.getContractNo());
        if(createAccountingResultDTO.getAvailableDate() != null) {
                requestApiReceiptDTO.setEffectiveDate((int) DateUtils.findReldayDiff(createAccountingResultDTO.getAvailableDate()));
        }

        requestApiReceiptDTO.setValueDate(createAccountingResultDTO.getAvailableDate());
        requestApiReceiptDTO.setProcessDate(CollectionUtils.isEmpty(createAccountingResultDTO.getPendingDetailList())? DateUtils.getCurrentDate(): createAccountingResultDTO.getPendingDetailList().get(1).getAccountingDate());
        requestApiReceiptDTO.setAmount(createAccountingDTO.getPaymentAmount());
        requestApiReceiptDTO.setUserCode(createAccountingDTO.getAgentCode());
        requestApiReceiptDTO.setChannel(createAccountingDTO.getChannelCode().equals("1658")? "501" : createAccountingDTO.getChannelCode());
        requestApiReceiptDTO.setDescription(prepareDescription(createAccountingDTO));
        requestApiReceiptDTO.setReverse("N");
        requestApiReceiptDTO.setInvoiceNoFlag(false);
        requestApiReceiptDTO.setProcessID(createAccountingDTO.getChannelTransactionId());
        requestApiReceiptDTO.setBasilacak("Y");

        List<RequestReceiptDetailDTO> receiptDetailList = new ArrayList<>();

        RequestReceiptDetailDTO accountNoLabel = new RequestReceiptDetailDTO();
        accountNoLabel.setFieldOrder(499);
        accountNoLabel.setValue(EnumReceiptSourceType.ACCOUNT_NO.getCode());
        accountNoLabel.setProcessCode(AccountingConstant.DKNTSABIT);
        accountNoLabel.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(accountNoLabel);

        RequestReceiptDetailDTO accountNoValue = new RequestReceiptDetailDTO();
        accountNoValue.setFieldOrder(999);
        accountNoValue.setValue(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO().getInstitutionAccountNo());
        accountNoValue.setProcessCode(AccountingConstant.DKNTSABIT);
        accountNoValue.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(accountNoValue);

        RequestReceiptDetailDTO paymentAmountLabel = new RequestReceiptDetailDTO();
        paymentAmountLabel.setFieldOrder(498);
        paymentAmountLabel.setValue(EnumReceiptSourceType.PROCESS_AMOUNT.getCode());
        paymentAmountLabel.setProcessCode(AccountingConstant.DKNTSABIT);
        paymentAmountLabel.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(paymentAmountLabel);

        RequestReceiptDetailDTO paymentAmountValue = new RequestReceiptDetailDTO();
        paymentAmountValue.setFieldOrder(998);
        paymentAmountValue.setFieldType("N");
        paymentAmountValue.setAmttype("AMT");
        paymentAmountValue.setValue(AccountingUtil.formatCurrency(createAccountingDTO.getPaymentAmount().toString()));
        paymentAmountValue.setNumericValue(createAccountingDTO.getPaymentAmount());
        paymentAmountValue.setProcessCode(AccountingConstant.DKNTSABIT);
        paymentAmountValue.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(paymentAmountValue);

        RequestReceiptDetailDTO paymentCurrencyLabel = new RequestReceiptDetailDTO();
        paymentCurrencyLabel.setFieldOrder(497);
        paymentCurrencyLabel.setValue(EnumReceiptSourceType.CURRENCY.getCode());
        paymentCurrencyLabel.setProcessCode(AccountingConstant.DKNTSABIT);
        paymentCurrencyLabel.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(paymentCurrencyLabel);

        RequestReceiptDetailDTO paymentCurrencyValue = new RequestReceiptDetailDTO();
        paymentCurrencyValue.setFieldOrder(997);
        paymentCurrencyValue.setValue( CurrencyUtil.currencyConverter(createAccountingDTO.getCurrency().getValue()));
        paymentCurrencyLabel.setProcessCode(AccountingConstant.DKNTSABIT);
        paymentCurrencyLabel.setContractNo(createAccountingResultDTO.getContractNo());
        receiptDetailList.add(paymentCurrencyValue);

        requestApiReceiptDTO.setReceiptDetailList(receiptDetailList);
        if(CollectionUtils.isEmpty(createAccountingResultDTO.getPendingDetailList())){
            return requestApiReceiptDTO;
        }
        List<Long> pendingTransactionIdList = new ArrayList<>();
        for (ProvisionDetailDTO provisionDetailDTO : createAccountingResultDTO.getPendingDetailList()) {
            if (provisionDetailDTO.getAmount().compareTo(BigDecimal.ZERO)>0  && !provisionDetailDTO.isCommission()
                    && !provisionDetailDTO.isCommissionTax()) {
                pendingTransactionIdList.add(provisionDetailDTO.getPendingTransactionDetailId());
                break;
            }
        }
        requestApiReceiptDTO.setPendingTransactionIdList(pendingTransactionIdList);

        return requestApiReceiptDTO;
    }

}

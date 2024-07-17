shouldHandleFailureIfMakeProvisionFails unitinde "java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO.getInstitutionAccountNo()" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChnnlPymMthdAccDTO()" is null


" 

shouldHandleErrorIfContractNoIsNull unitinnde java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO.getInstitutionAccountNo()" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChnnlPymMthdAccDTO()" is null


" 

shouldHandleExceptionAndReturnError unitinde "
org.mockito.exceptions.base.MockitoException: 
Checked exception is invalid for this method!
Invalid: com.ykb.architecture.micro.error.exception.ServiceCallException"


private CreateAccountingDTO createSampleDTO() {
    CreateAccountingDTO dto = new CreateAccountingDTO();
    dto.setChannelTransactionId("123");

    // Create ProvisionDTO if it's null
    if (dto.getProvisionDTO() == null) {
        dto.setProvisionDTO(new ProvisionDTO());
    }

    dto.getProvisionDTO().setCustomerNo("1234567890");  // Or set any necessary fields

    // Create InstitutionChannelPymMethodDTO if it's null
    if (dto.getInstitutionChannelPymMethodDTO() == null) {
        dto.setInstitutionChannelPymMethodDTO(new InstitutionChannelPymMethodDTO());
    }

    dto.getInstitutionChannelPymMethodDTO().setAccountingTemplateCode("template");
    dto.setChannelCode("channel");
    dto.setAgentCode("agent");
    dto.setBranchCode("branch");
    dto.setCurrency(EnumCurrencyCode.DOLAR);
    dto.setPaymentAmount(BigDecimal.TEN);

    AccountPaymentMethodDetailDTO paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
    paymentMethodDetailDTO.setAccountNo("1234567890");
    dto.setPaymentMethodDetailDTO(paymentMethodDetailDTO);

    return dto;
}


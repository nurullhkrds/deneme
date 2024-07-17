shouldHandleFailureIfMakeProvisionFails unitinde "java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO.setAccountingTemplateCode(String)" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChannelPymMethodDTO()" is null
" 

shouldHandleErrorIfContractNoIsNull unitinnde "java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO.setAccountingTemplateCode(String)" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChannelPymMethodDTO()" is null
" 

shouldHandleExceptionAndReturnError unitinde "java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO.setAccountingTemplateCode(String)" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChannelPymMethodDTO()" is null
" hatasını verdi
private CreateAccountingDTO createSampleDTO() {
    CreateAccountingDTO dto = new CreateAccountingDTO();
    dto.setChannelTransactionId("123");

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

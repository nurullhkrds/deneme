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

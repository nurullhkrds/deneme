@Test
void testPrintReceiptWithEmptyPendingDetailList() {
    // Arrange
    CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
    CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

    // Set valid receipt code to avoid IllegalArgumentException
    createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.ACCOUNT);
    createAccountingDTO.setPaymentMethodDetailDTO(new AccountPaymentMethodDetailDTO());
    createAccountingDTO.setBranchCode("003");
    createAccountingDTO.setAgentCode("AGENT003");
    createAccountingDTO.setChannelCode("CH003");
    createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
    createAccountingDTO.setProvisionDTO(new ProvisionDTO());
    createAccountingDTO.getProvisionDTO().setCustomerNo(1003L);

    // Ensure institution and receipt code are valid
    InstitutionAccountingInfoDTO institutionDTO = new InstitutionAccountingInfoDTO();
    institutionDTO.setReceiptCode("VALID_RECEIPT_CODE"); // Set valid receipt code here
    createAccountingDTO.setInstitutionAccountingInfoDTO(institutionDTO);

    createAccountingResultDTO.setContractNo(345678901L);
    createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(300.00));
    createAccountingResultDTO.setPendingDetailList(Collections.emptyList());

    // Act
    receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

    // Assert
    verify(receiptApiService).printReceipt(any(List.class));
}

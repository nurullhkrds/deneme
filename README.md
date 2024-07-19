@Test
void testPrintReceiptWithEmptyPendingDetailList() {
    // Arrange
    CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
    CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

    // Initialize InstitutionDTO to avoid NullPointerException
    InstitutionDTO institutionDTO = new InstitutionDTO();
    institutionDTO.setName("Sample Institution"); // Set a sample name or relevant data

    createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.ACCOUNT);
    createAccountingDTO.setPaymentMethodDetailDTO(new AccountPaymentMethodDetailDTO());
    createAccountingDTO.setBranchCode("003");
    createAccountingDTO.setAgentCode("AGENT003");
    createAccountingDTO.setChannelCode("CH003");
    createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
    createAccountingDTO.setProvisionDTO(new ProvisionDTO());
    createAccountingDTO.getProvisionDTO().setCustomerNo(1003L);
    createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
    createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("RECEIPT_CODE_1"); // Use a valid enum value
    createAccountingDTO.setInstitution(institutionDTO); // Set the institution field

    createAccountingResultDTO.setContractNo(345678901L);
    createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(300.00));
    createAccountingResultDTO.setPendingDetailList(Collections.emptyList());

    // Ensure valid Date values for other fields
    createAccountingResultDTO.setAvailableDate(new Date()); // Set a valid date

    // Act
    receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

    // Assert
    verify(receiptApiService).printReceipt(any(List.class));
}

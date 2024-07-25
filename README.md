@Test
public void testPerformCommission_CreditCardSuccess() throws Exception {
    // Prepare mock objects
    ProvisionDTO provisionDTO = new ProvisionDTO();
    provisionDTO.setInstitutionDebtTypeId(123L);
    provisionDTO.setCustomerNo(123L);

    InstitutionDebtTypeDTO debtTypeDTO = new InstitutionDebtTypeDTO();
    debtTypeDTO.setInstitution(new InstitutionDTO());
    debtTypeDTO.getInstitution().setProduct(new ProductDTO());
    debtTypeDTO.getInstitution().getProduct().setCode("productCode");
    debtTypeDTO.getInstitution().setInstitutionCode("institutionCode");

    InstitutionPymMethodWebDTO pymMethodDTO = new InstitutionPymMethodWebDTO();
    pymMethodDTO.setExpenseCode("expenseCode");
    pymMethodDTO.setExpenseAccountNo("accountNo");
    pymMethodDTO.setExpenseType(EnumExpenseType.FROM_CUSTOMER.getValue());

    ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
    // Mock the behavior of services
    when(provisionService.getProvisionRecord(any())).thenReturn(provisionDTO);
    when(institutionDebtTypeService.getDebtType(any())).thenReturn(debtTypeDTO);
    when(instPaymentMethodService.getInstitutionExpenseCode(any(), any(), any(), any(), any())).thenReturn(pymMethodDTO);
    when(commissionService.inquireCommission(any())).thenReturn(responseCommissionInformation);

    // Prepare request DTO
    CommissionServiceRequestDTO requestDTO = new CommissionServiceRequestDTO();
    requestDTO.setBillProvisionId(123L);
    requestDTO.setPaymentMethod(EnumPaymentMethod.CARD.getValue());
    requestDTO.setPaymentAmount(BigDecimal.TEN);
    requestDTO.setPaymentCurrency("TRY");
    requestDTO.setChannelCode("channelCode");
    requestDTO.setAccountBranchCode("branchCode");

    // Call the method under test
    ResponseCommissionInformation result = paymentCommissionServiceImpl.performCommission(requestDTO);

    // Verify the result
    assertEquals(responseCommissionInformation, result);

    // Verify that prepareCommissionInformationReqForCreditCard was used to create request
    ArgumentCaptor<RequestCommissionInformation> captor = ArgumentCaptor.forClass(RequestCommissionInformation.class);
    verify(commissionService).inquireCommission(captor.capture());

    RequestCommissionInformation capturedRequest = captor.getValue();
    assertEquals(CREDIT_CARD, capturedRequest.getAccountingType());
    assertNull(capturedRequest.getAccountNo());
    assertNull(capturedRequest.getAccountCurrency());
    assertEquals(BigDecimal.TEN, capturedRequest.getTransactionAmount());
    assertEquals("YTL", capturedRequest.getTransactionCurrency());
    assertEquals("channelCode", capturedRequest.getChannelCode());
    assertEquals(BRANCH_CODE_925, capturedRequest.getBranch());
    assertEquals(USER_CODE_INT001, capturedRequest.getUserCode());
    assertEquals(1, capturedRequest.getCommissionInputDetailApiDTOList().size());
    CommissionInputDetailApiDTO detail = capturedRequest.getCommissionInputDetailApiDTOList().get(0);
    assertEquals("expenseCode", detail.getOperationCode());
    assertEquals("YTL", detail.getCommissionCurrency());
}

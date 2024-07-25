@Test
public void testPerformCommission_Success() throws Exception {
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
    requestDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT.getValue());
    requestDTO.setPaymentAmount(BigDecimal.TEN);
    requestDTO.setPaymentCurrency("TRY");
    requestDTO.setChannelCode("channelCode");
    requestDTO.setAccountBranchCode("branchCode");

    // Call the method under test
    ResponseCommissionInformation result = paymentCommissionServiceImpl.performCommission(requestDTO);

    // Verify the result
    assertEquals(responseCommissionInformation, result);
    verify(provisionService).updateCommissionData(anyString(), anyLong());
}

@Test
public void testPerformCommission_NoInstitutionPaymentMethod() throws Exception {
    // Prepare mock objects
    ProvisionDTO provisionDTO = new ProvisionDTO();
    provisionDTO.setInstitutionDebtTypeId(123L);

    InstitutionDebtTypeDTO debtTypeDTO = new InstitutionDebtTypeDTO();
    debtTypeDTO.setInstitution(new InstitutionDTO());
    debtTypeDTO.getInstitution().setProduct(new ProductDTO());
    debtTypeDTO.getInstitution().getProduct().setCode("productCode");
    debtTypeDTO.getInstitution().setInstitutionCode("institutionCode");

    // Mock the behavior of services
    when(provisionService.getProvisionRecord(any())).thenReturn(provisionDTO);
    when(institutionDebtTypeService.getDebtType(any())).thenReturn(debtTypeDTO);
    when(instPaymentMethodService.getInstitutionExpenseCode(any(), any(), any(), any(), any())).thenReturn(null);

    // Prepare request DTO
    CommissionServiceRequestDTO requestDTO = new CommissionServiceRequestDTO();
    requestDTO.setBillProvisionId(123L);
    requestDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT.getValue());

    // Call the method under test
    ResponseCommissionInformation result = paymentCommissionServiceImpl.performCommission(requestDTO);

    // Verify the result
    assertNull(result);
}

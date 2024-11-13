@Test
void updateInstitutionPymMethod_WhenValid_ShouldReturnDTO() throws MicroException {
    UpdateInstitutionPymMethodRequestDTO requestDTO = new UpdateInstitutionPymMethodRequestDTO();
    requestDTO.setId(1L);
    requestDTO.setInstitutionId(1L);
    requestDTO.setPaymentMethod(EnumPaymentMethod.CARD);
    requestDTO.setExpenseCode("EXP123");
    requestDTO.setIsActive(true);
    requestDTO.setUpdateUser("testUser");

    InstitutionPymMethod existingPymMethod = new InstitutionPymMethod();
    existingPymMethod.setId(1L);

    InstitutionDTO institutionDTO = new InstitutionDTO();
    institutionDTO.setId(1L);

    PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();

    InstitutionPymMethodDTO pymMethodDTO = new InstitutionPymMethodDTO();
    pymMethodDTO.setId(1L);
    pymMethodDTO.setInstitution(institutionDTO);
    pymMethodDTO.setPaymentMethod(paymentMethodDTO);

    when(institutionPymMethodRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingPymMethod));
    when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue())).thenReturn(Optional.empty());
    when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
    when(paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod())).thenReturn(paymentMethodDTO);
    when(institutionPymMethodMapper.toDTO(existingPymMethod)).thenReturn(pymMethodDTO);
    when(institutionPymMethodMapper.toInstitutionPymMethod(pymMethodDTO)).thenReturn(existingPymMethod);
    when(institutionPymMethodRepository.save(existingPymMethod)).thenReturn(existingPymMethod);
    when(institutionPymMethodMapper.toDTO(existingPymMethod)).thenReturn(pymMethodDTO);

    InstitutionPymMethodDTO result = adminInstitutionPymMethodService.updateInstitutionPymMethod(requestDTO);

    assertNotNull(result);
    assertEquals(institutionDTO, result.getInstitution());
    assertEquals(paymentMethodDTO, result.getPaymentMethod());
    assertEquals("EXP123", result.getExpenseCode());
    assertTrue(result.getIsActive());
    verify(institutionPymMethodRepository, times(1)).findById(requestDTO.getId());
    verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue());
    verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    verify(paymentMethodService, times(1)).getPaymentMethodByMethod(requestDTO.getPaymentMethod());
    verify(institutionPymMethodRepository, times(1)).save(existingPymMethod);
}

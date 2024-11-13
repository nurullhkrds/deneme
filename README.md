 @Test
    void createInstitutionPymMethod_WhenValid_ShouldReturnDTO() throws MicroException {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(1L);
        requestDTO.setPaymentMethod(EnumPaymentMethod.CARD);
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionPymMethodDTO pymMethodDTO = new InstitutionPymMethodDTO();
        pymMethodDTO.setInstitution(institutionDTO);
        pymMethodDTO.setPaymentMethod(paymentMethodDTO);
        InstitutionPymMethod pymMethod = new InstitutionPymMethod();

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(any(), any())).thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(anyLong())).thenReturn(institutionDTO);
        when(paymentMethodService.getPaymentMethodByMethod(any())).thenReturn(paymentMethodDTO);
        when(institutionPymMethodMapper.toDTO((InstitutionPymMethod) any())).thenReturn(pymMethodDTO);
        when(institutionPymMethodMapper.toInstitutionPymMethod(any())).thenReturn(pymMethod);
        when(institutionPymMethodRepository.save(any())).thenReturn(pymMethod);
        when(institutionPymMethodMapper.toDTO(pymMethod)).thenReturn(pymMethodDTO);

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO);

        assertNotNull(result);
        verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(any(), any());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(anyLong());
        verify(paymentMethodService, times(1)).getPaymentMethodByMethod(any());
        verify(institutionPymMethodRepository, times(1)).save(any());
    }


java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionPymMethodDTO.setInstitution(com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO)" because "institutionPymMethodDTO" is null

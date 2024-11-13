java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminCityService.getCityByCode(String)" because "this.cityService" is null
  @Test
    void createInstitutionCity_shouldReturnInstitutionCityDTO_whenSuccessful() throws MicroException {
        CreateInstitutionCityRequestDTO requestDTO = new CreateInstitutionCityRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setCode("MALATYA");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        CityDTO cityDTO = new CityDTO();
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        InstitutionCity institutionCity = new InstitutionCity();

        when(institutionCityRepository.findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(cityService.getCityByCode(requestDTO.getCode())).thenReturn(cityDTO);
        when(institutionCityMapper.toDTO(requestDTO)).thenReturn(institutionCityDTO);
        when(institutionCityMapper.toInstitutionCity(institutionCityDTO)).thenReturn(institutionCity);
        when(institutionCityRepository.save(institutionCity)).thenReturn(institutionCity);
        when(institutionCityMapper.toInstitutionCityDTO(institutionCity)).thenReturn(institutionCityDTO);

        InstitutionCityDTO result = adminInstitutionCityService.createInstitutionCity(requestDTO);

        assertNotNull(result);
        assertEquals(institutionCityDTO, result);
        verify(institutionCityRepository, times(1)).findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(cityService, times(1)).getCityByCode(requestDTO.getCode());
        verify(institutionCityMapper, times(1)).toDTO(requestDTO);
        verify(institutionCityMapper, times(1)).toInstitutionCity(institutionCityDTO);
        verify(institutionCityRepository, times(1)).save(institutionCity);
        verify(institutionCityMapper, times(1)).toInstitutionCityDTO(institutionCity);
    }

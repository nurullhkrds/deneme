 // InstitutionCityDTO listesini dönüştürür
    List<InstitutionCityWebDTO> toWebDTOList(List<InstitutionCityDTO> dtoList);

    // Tek bir InstitutionCityDTO'yu InstitutionCityWebDTO'ya dönüştürür
    @Mapping(source = "institution.id", target = "institutionId")
    @Mapping(source = "institution.name", target = "institutionName")
    @Mapping(source = "city.name", target = "city")
    InstitutionCityWebDTO toWebDTO(InstitutionCityDTO dto);

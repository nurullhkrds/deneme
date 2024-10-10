@Override
public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
    Specification<ReturnMap> spec = Specification.where(null);

    if (returnMapCode != null && !returnMapCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode));
    }
    if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
    }
    if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
    }

    List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);

    // Tek seferde institutions listesi alınıyor
    List<String> institutions = returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode);

    List<ReturnMapDTO> returnMapDTOList = returnMapList.stream()
            .map(entity -> {
                ReturnMapDTO dto = returnMapMapper.toReturnMapDTO(entity);
                if (dto != null && dto.getReturnMapDefinition() != null) {
                    dto.getReturnMapDefinition().setInstitutions(institutions); // Tüm DTO'lara aynı institutions listesi set ediliyor
                }
                return dto;
            })
            .collect(Collectors.toList());

    if (returnMapDTOList.isEmpty()) {
        return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
    }

    return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
}

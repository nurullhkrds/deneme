@Mapper(componentModel = "spring", uses = {ReturnMapDefinitionMapper.class})
public interface ReturnMapMapper {

    ReturnMapDTO toReturnMapDTO(ReturnMap entity);
    
    ReturnMap toReturnMap(ReturnMapDTO dto);
    
    List<ReturnMapDTO> toReturnMapDTOList(List<ReturnMap> entityList);

    // Institutions verisini set eden default method
    default ReturnMapDTO toReturnMapDTOWithInstitutions(ReturnMap entity, ReturnMapDefinitionRepository returnMapDefinitionRepository) {
        ReturnMapDTO dto = toReturnMapDTO(entity);

        if (dto != null && dto.getReturnMapDefinition() != null) {
            String returnMapCode = dto.getReturnMapDefinition().getReturnMapCode();
            List<String> institutions = returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode);
            dto.getReturnMapDefinition().setInstitutions(institutions);
        }

        return dto;
    }
}
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
    
    // Institutions'ları dolduran methodu kullanıyoruz
    List<ReturnMapDTO> returnMapDTOList = returnMapList.stream()
            .map(entity -> returnMapMapper.toReturnMapDTOWithInstitutions(entity, returnMapDefinitionRepository))
            .collect(Collectors.toList());

    if (returnMapDTOList.isEmpty()) {
        return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
    }

    return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
}

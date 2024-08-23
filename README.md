@Query(value = "SELECT i.* FROM INSTITUTION i " +
               "WHERE i.id IN (SELECT ia.institution_id " +
               "FROM INSTITUTION_ADAPTER ia " +
               "JOIN ADAPTER_SERVICE as ON ia.adapter_id = as.adapter_id " +
               "JOIN SERVICE s ON as.service_id = s.id " +
               "WHERE s.return_map_code = :returnMapCode)", 
       nativeQuery = true)
List<Institution> findInstitutionsByReturnMapCode(@Param("returnMapCode") String returnMapCode);



@Override
public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionByReturnMapCode(String returnMapCode) {
    Optional<ReturnMapDefinition> optionalReturnMapDefinition = returnMapDefinitionRepository.findByReturnMapCode(returnMapCode);

    if (optionalReturnMapDefinition.isPresent()) {
        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(optionalReturnMapDefinition.get());

        // Kurumları çekmek için native query kullanın
        List<Institution> institutions = returnMapDefinitionRepository.findInstitutionsByReturnMapCode(returnMapCode);

        if (dto != null) {
            dto.setInstitutions(institutions); // DTO'ya kurumları ekleyin
            return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto, 200);
        } else {
            return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
        }
    } else {
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 200);
    }
}


    private List<Institution> institutions;

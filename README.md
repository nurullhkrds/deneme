@Transactional
public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
    // ReturnMapDefinition kontrolü
    DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());
    if (request.getReturnMapDefinitionId() != null && !definitionDataResult.isSuccess()) {
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 400);
    }

    // Varolan kaydı kontrol et (RETURN_MAP_DEFINITION_ID ve INSTITUTION_RETURN_CODE kombinasyonu)
    Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
            request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

    if (existingReturnMap.isPresent()) {
        // Aynı kombinasyon varsa, hata döndür
        return new ErrorDataResult<>("Bu institution_code ve return_map_definition_id ile zaten bir kayıt mevcut.", null, 400);
    }

    // Yeni kayıt oluşturma işlemi
    ReturnMap returnMap = new ReturnMap();
    returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
    returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
    returnMap.setCreatedBy(request.getCreateUser());
    returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
    returnMap.setBankReturnCode(request.getBankReturnCode());
    returnMap.setBankReturnText(request.getBankReturnText());
    returnMap.setIsReversible(request.getIsReversible());
    returnMap.setReturnMapDefinition(definitionDataResult.getData());
    returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS : EnumReturnType.ERROR);
    
    // Kayıt veritabanına kaydedilir
    ReturnMap result = returnMapRepository.save(returnMap);
    ReturnMapDTO resultDto = returnMapMapper.toReturnMapDTO(result);
    
    if (resultDto != null) {
        return new SuccessDataResult<>("ReturnMap Added", resultDto, 200);
    }
    return new ErrorDataResult<>("ReturnMap not be added", null, 400);
}



public interface ReturnMapRepository extends JpaRepository<ReturnMap, Long> {

    @Query(value = "SELECT * FROM BILL.RETURN_MAP rm WHERE rm.return_map_definition_id = :returnMapDefinitionId AND rm.institution_return_code = :institutionReturnCode", nativeQuery = true)
    Optional<ReturnMap> findByReturnMapDefinitionIdAndInstitutionReturnCode(@Param("returnMapDefinitionId") Long returnMapDefinitionId, @Param("institutionReturnCode") String institutionReturnCode);

}

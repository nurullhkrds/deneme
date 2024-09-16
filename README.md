@Transactional
public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) {
    // ReturnMap kaydını getirme
    DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
    // ReturnMapDefinition kontrolü
    DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

    if (request.getReturnMapDefinitionId() != null && !definitionDataResult.isSuccess()) {
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 400);
    }

    if (!returnMapDataResult.isSuccess()) {
        return new ErrorDataResult<>("ReturnMap not be updated", null, 400);
    }

    // Güncellenen kaydın ID'si haricinde, aynı RETURN_MAP_DEFINITION_ID ve INSTITUTION_RETURN_CODE kombinasyonuna sahip başka bir kayıt olup olmadığını kontrol et
    Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
            request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

    if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
        // Eğer aynı kombinasyona sahip başka bir kayıt varsa, güncelleme işlemi yapılmasın
        return new ErrorDataResult<>("Bu institution_code ve return_map_definition_id ile zaten başka bir kayıt mevcut.", null, 400);
    }

    // Güncelleme işlemi
    ReturnMap returnMap = returnMapDataResult.getData();
    returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
    returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
    returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
    returnMap.setBankReturnCode(request.getBankReturnCode());
    returnMap.setUpdatedBy(request.getUpdateUser());
    returnMap.setBankReturnText(request.getBankReturnText());
    returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS : EnumReturnType.ERROR);
    returnMap.setIsReversible(request.getIsReversible());
    returnMap.setReturnMapDefinition(definitionDataResult.getData());

    // Kaydı güncelle
    ReturnMap result = returnMapRepository.save(returnMap);
    ReturnMapDTO resultDto = returnMapMapper.toReturnMapDTO(result);

    if (resultDto != null) {
        return new SuccessDataResult<>("ReturnMap updated", resultDto, 200);
    }
    return new ErrorDataResult<>("ReturnMap not be updated", null, 400);
}

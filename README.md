@Override
public DataResult<ReturnMapDefinitionDTO> updateReturnMapDefinition(UpdateReturnMapDefinitionRequest request) {
    
    DataResult<ReturnMapDefinition> returnMapDefinitionDataResult = getReturnMapDefinitionByIdForServices(request.getId());
    
    if (returnMapDefinitionDataResult.isSuccess()) {
        ReturnMapDefinition changedData = returnMapDefinitionDataResult.getData();

        // Aynı returnMapCode'ye sahip başka bir kayıt olup olmadığını kontrol edin
        Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
            // Eğer başka bir kayıt varsa ve bu kayıt güncellenecek kayıttan farklıysa hata döndür
            return new ErrorDataResult<>(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), null, 400);
        }

        // Güncellenen veriyi ayarla
        changedData.setIsActive(request.getIsActive());
        changedData.setReturnMapCode(request.getReturnMapCode());

        ReturnMapDefinition updatedData = returnMapDefinitionRepository.save(changedData);
        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(updatedData);

        if (dto == null) {
            return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
        }

        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), dto, 200);
    }

    return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 400);
}

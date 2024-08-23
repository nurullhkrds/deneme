 @Override
    public DataResult<ReturnMapDefinitionDTO> updateReturnMapDefinition(UpdateReturnMapDefinitionRequest request) {
        
        DataResult<ReturnMapDefinition> returnMapDefinitionDataResult=getReturnMapDefinitionByIdForServices(request.getId());
        if (returnMapDefinitionDataResult.isSuccess()){
            ReturnMapDefinition changedData=returnMapDefinitionDataResult.getData();
            changedData.setIsActive(request.getIsActive());
            changedData.setReturnMapCode(request.getReturnMapCode());

            ReturnMapDefinition updatedData=returnMapDefinitionRepository.save(changedData);
            ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(updatedData);
            if (dto == null) {
                return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
            }

            return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), dto,200);

        }
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
    }

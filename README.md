    @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionById(Long id) {
        Optional<ReturnMapDefinition> returnMapDefinitionOptional=returnMapDefinitionRepository.findById(id);

        if (returnMapDefinitionOptional.isPresent()){
            ReturnMapDefinitionDTO dto= returnMapDefinitionMapper.toReturnMapDefinitionDTO(returnMapDefinitionOptional.get());
            return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto,200);
        }
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
    }

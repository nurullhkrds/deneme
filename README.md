    @Override
    public DataResult<List<ReturnMapDefinitionDTO>> getAllReturnMapDefinitionByReturnMapCode(String returnMapCode) {
        Optional<List<ReturnMapDefinition>> optionalReturnMapDefinitions=returnMapDefinitionRepository.findAllByReturnMapCode(returnMapCode);
        List<ReturnMapDefinitionDTO> dtoList=returnMapDefinitionMapper.toReturnMapDefinitionDTOList(optionalReturnMapDefinitions.get());
        if (dtoList !=null){
            return new SuccessDataResult<>(Result.DATA_LISTED.getMessage(), dtoList,200);
        }
        return new ErrorDataResult<>(Result.CONVERSION_FAILED.getMessage(), null,500);
    }

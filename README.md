@Override
public DataResult<List<ReturnMapDefinitionDTO>> getAllReturnMapDefinitionByReturnMapCode(String returnMapCode) {
    Optional<List<ReturnMapDefinition>> optionalReturnMapDefinitions = returnMapDefinitionRepository.findAllByReturnMapCode(returnMapCode);

    if (optionalReturnMapDefinitions.isPresent()) {
        List<ReturnMapDefinitionDTO> dtoList = returnMapDefinitionMapper.toReturnMapDefinitionDTOList(optionalReturnMapDefinitions.get());

        if (dtoList != null && !dtoList.isEmpty()) {
            return new SuccessDataResult<>(dtoList, Result.DATA_LISTED.getMessage(), 200);
        } else {
            return new ErrorDataResult<>(Result.CONVERSION_FAILED.getMessage(), null, 500);
        }
    } else {
        return new ErrorDataResult<>(Result.RECORD_ALREADY_EXISTS.getMessage(), null, 404);
    }
}

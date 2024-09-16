 @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) throws DataConflictException, DataNotFoundException {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
        DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

        if (request.getReturnMapDefinitionId() != null && !definitionDataResult.isSuccess()) {
            ExceptionData error=new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.RECORD_NOT_FOUND.getMessage());
            error.setApplicationName("ReturnMapService");
            throw new DataNotFoundException(error);
        }

        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

        if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
            ExceptionData error=new ExceptionData();
            error.setErrorCode(409L);
            error.setErrorMessage(ResultConstant.RECORD_NOT_FOUND.getMessage());
            error.setApplicationName("ReturnMapService");
            throw  new DataConflictException(Long.valueOf(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage())) ;
        }

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

        ReturnMap result = returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto = returnMapMapper.toReturnMapDTO(result);

        if (resultDto != null) {
            return new SuccessDataResult<>("ReturnMap updated", resultDto, 200);
        }
        return new ErrorDataResult<>("ReturnMap not be updated", null, 400);
    }

  @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
        DataResult<ReturnMapDefinition> definitionDataResult=returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

        if (request.getReturnMapDefinitionId()!=null && !definitionDataResult.isSuccess()){
            return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
        }

        if (!returnMapDataResult.isSuccess()){
            return new ErrorDataResult<>("ReturnMap not be updated",null,400);
        }


        ReturnMap returnMap = returnMapDataResult.getData();

        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setUpdatedBy(request.getUpdateUser());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());

        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);

        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap updated",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be updated",null,400);
    }

    @Transactional
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
       DataResult<ReturnMapDefinition> definitionDataResult=returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());
       if (request.getReturnMapDefinitionId()!=null && !definitionDataResult.isSuccess()){
           return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
       }

        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setCreatedBy(request.getCreateUser());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);
        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap Added",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be added",null,400);
    }

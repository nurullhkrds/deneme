   @Test
    void testUpdateReturnMap_DefinitionNotFound() {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);

        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error", null, 400);

        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        Assertions.assertThrows(DataNotFoundException.class, () -> {
            returnMapService.updateReturnMap(request);
        });
    }


    @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) throws DataConflictException, DataNotFoundException {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
        DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

        if (request.getReturnMapDefinitionId() != null && definitionDataResult==null) {

            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.RETURN_MAP_DEFINITION_NOT_FOUND);
        }

        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

        if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
            throw  new DataConflictException(BillExceptionsUI.ValidationExceptions.RETURN_MAP_AND_INSTITUTION_EXIST) ;

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
        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), resultDto, 200);

    }

org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <com.ykb.architecture.micro.error.exception.DataNotFoundException> but was: <java.lang.NullPointerException>

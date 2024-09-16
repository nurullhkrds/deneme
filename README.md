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
            throw  new DataConflictException(error) ;
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


  @Test
     void testUpdateReturnMap_Success() throws DataNotFoundException, DataConflictException {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");
        request.setInstitutionReturnText("Text");
        request.setBankReturnCode("456");
        request.setBankReturnText("Bank Text");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");
        request.setUpdateUser("User");

        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        returnMapDefinition.setReturnMapCode("MapCode");

        DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>("succes",returnMapDefinition,200);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        ReturnMap returnMap = new ReturnMap();
        returnMap.setId(1L);

        Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.of(returnMap));

        Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        assertTrue(result.isSuccess());
        assertNotNull(result.getData());
    }

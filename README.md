 @Test
    public void testCreateReturnMap_Success() throws DataNotFoundException, DataConflictException {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");
        request.setInstitutionReturnText("Text");
        request.setBankReturnCode("456");
        request.setBankReturnText("Bank Text");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");
        request.setCreateUser("User");

        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        returnMapDefinition.setReturnMapCode("MapCode");

        DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);

        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.empty());

        ReturnMap returnMap = new ReturnMap();
        Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getCode());
        assertNotNull(result.getData());
    }

    @Test(expected = DataConflictException.class)
    public void testCreateReturnMap_Conflict() throws DataNotFoundException, DataConflictException {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");

        ReturnMap returnMap = new ReturnMap();

        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.of(returnMap));

        returnMapService.createReturnMap(request); // Should throw DataConflictException
    }

    @Test(expected = DataNotFoundException.class)
    public void testCreateReturnMap_DefinitionNotFound() throws DataNotFoundException, DataConflictException {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);

        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>(null);

        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        returnMapService.createReturnMap(request); // Should throw DataNotFoundException
    }

@Test
public void testUpdateReturnMap_Success() throws DataNotFoundException, DataConflictException {
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

    DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);
    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(definitionDataResult);

    ReturnMap returnMap = new ReturnMap();
    returnMap.setId(1L);

    DataResult<ReturnMap> returnMapDataResult = new SuccessDataResult<>(returnMap);
    Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

    Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
            .thenReturn(Optional.of(returnMap));

    Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

    ReturnMapDTO returnMapDTO = new ReturnMapDTO();
    Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

    DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getCode());
    assertNotNull(result.getData());
}

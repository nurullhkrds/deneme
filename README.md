@Test
void testUpdateReturnMap_Success() throws DataNotFoundException, DataConflictException {
    // Arrange
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

    // Mocking ReturnMapDefinitionService to return SuccessDataResult
    ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
    returnMapDefinition.setReturnMapCode("MapCode");
    DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>("success", returnMapDefinition, 200);
    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(definitionDataResult);

    // Mocking returnMapRepository to return an existing ReturnMap
    ReturnMap returnMap = new ReturnMap();
    returnMap.setId(1L);
    Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

    // Mocking save operation and mapping
    Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);
    ReturnMapDTO returnMapDTO = new ReturnMapDTO();
    Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

    // Act
    DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

    // Assert
    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertNotNull(result.getData());
}

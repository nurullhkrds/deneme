@Test
public void testCreateReturnMap_Conflict() throws DataNotFoundException, DataConflictException {
    // Arrange
    CreateReturnMapRequest request = new CreateReturnMapRequest();
    request.setReturnMapDefinitionId(1L);
    request.setInstitutionReturnCode("123");

    // Mock the returnMapRepository to return an existing ReturnMap, causing a conflict
    ReturnMap existingReturnMap = new ReturnMap();
    Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
            .thenReturn(Optional.of(existingReturnMap));

    // Mock the returnMapDefinitionService to return a valid DataResult (not null)
    ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
    DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);
    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(definitionDataResult);

    // Act & Assert
    // Expecting a DataConflictException to be thrown
    Assertions.assertThrows(DataConflictException.class, () -> {
        returnMapService.createReturnMap(request);
    });
}

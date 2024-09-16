@Test
public void testCreateReturnMap_DefinitionNotFound() throws DataNotFoundException, DataConflictException {
    // Arrange
    CreateReturnMapRequest request = new CreateReturnMapRequest();
    request.setReturnMapDefinitionId(1L);

    // Mock the return value to simulate a definition not found scenario
    DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error", null, 400);

    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(definitionDataResult);

    // Act & Assert
    // Expect DataNotFoundException to be thrown
    Assertions.assertThrows(DataNotFoundException.class, () -> {
        returnMapService.createReturnMap(request);
    });
}

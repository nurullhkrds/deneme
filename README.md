@Test
void testUpdateReturnMap_DefinitionNotFound() throws DataNotFoundException, DataConflictException {
    // Arrange
    UpdateReturnMapRequest request = new UpdateReturnMapRequest();
    request.setId(1L);
    request.setReturnMapDefinitionId(1L);

    // Mock the return to simulate definition not found
    DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error", null, 400);
    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(definitionDataResult);

    // Act & Assert
    DataNotFoundException exception = Assertions.assertThrows(DataNotFoundException.class, () -> {
        returnMapService.updateReturnMap(request);
    });

    // Verify ExceptionData fields in the thrown exception
    ExceptionData error = exception.getError();
    assertNotNull(error);  // Ensure ExceptionData is not null
    assertEquals(400L, error.getErrorCode());  // Validate error code
    assertEquals("ReturnMapService", error.getApplicationName());  // Validate application name
    assertEquals(ResultConstant.RECORD_NOT_FOUND.getMessage(), error.getErrorMessage());  // Validate error message
}

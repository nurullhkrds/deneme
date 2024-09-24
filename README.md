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

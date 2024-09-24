@Test
void testUpdateReturnMap_DefinitionNotFound() {
    UpdateReturnMapRequest request = new UpdateReturnMapRequest();
    request.setId(1L);
    request.setReturnMapDefinitionId(1L);

    // Mocking the service to return null when the method is called
    Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
            .thenReturn(null);

    // Expecting a DataNotFoundException to be thrown
    Assertions.assertThrows(DataNotFoundException.class, () -> {
        returnMapService.updateReturnMap(request);
    });
}

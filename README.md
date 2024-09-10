@Test
void testCreateReturnMapDefinition_RecordAlreadyExists() throws DataConflictException {
    CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
    request.setReturnMapCode("existingCode");

    when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.of(new ReturnMapDefinition()));

    assertThrows(DataConflictException.class, () -> {
        returnMapDefinitionService.createReturnMapDefinition(request);
    });
}

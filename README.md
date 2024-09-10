@Test
void testCreateReturnMapDefinition_RecordAlreadyExists() throws DataConflictException {
    CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
    request.setReturnMapCode("existingCode");

    ReturnMapDefinition existingReturnMap = new ReturnMapDefinition();
    when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.of(existingReturnMap));

    DataConflictException thrown = assertThrows(DataConflictException.class, () -> {
        returnMapDefinitionService.createReturnMapDefinition(request);
    });

    assertEquals(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), thrown.getMessage());
}

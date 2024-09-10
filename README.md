@Test
void testCreateReturnMapDefinition_RecordAlreadyExists() {
    CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
    request.setReturnMapCode("existingCode");

    // Mocking the repository to return an existing return map
    when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode()))
            .thenReturn(Optional.of(new ReturnMapDefinition()));

    // Expect the DataConflictException to be thrown
    DataConflictException thrown = assertThrows(DataConflictException.class, () -> {
        returnMapDefinitionService.createReturnMapDefinition(request);
    });

    // Asserting the exception message with the actual constant value
    assertEquals(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), thrown.getMessage());
}

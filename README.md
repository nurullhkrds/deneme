@Test
void testCreateReturnMapDefinition_RecordAlreadyExists() {
    CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
    request.setReturnMapCode("existingCode");

    // Mocking the repository to return an existing return map
    when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode()))
            .thenReturn(Optional.of(new ReturnMapDefinition()));

    // Mocking the constant to return a numeric string to avoid NumberFormatException
    when(ResultConstant.RECORD_ALREADY_EXISTS.getMessage()).thenReturn("123");

    // Expect the DataConflictException to be thrown
    DataConflictException thrown = assertThrows(DataConflictException.class, () -> {
        returnMapDefinitionService.createReturnMapDefinition(request);
    });

    // Asserting the exception message
    assertEquals("123", thrown.getMessage());
}

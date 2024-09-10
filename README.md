@Override
public DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(CreateReturnMapDefinitionRequest request) throws DataConflictException {
    Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

    if (existingReturnMap.isPresent()) {
        // Long.valueOf() yerine doğrudan hata mesajı ile exception fırlatıyoruz
        throw new DataConflictException(ResultConstant.RECORD_ALREADY_EXISTS.getMessage());
    }

    ReturnMapDefinition newReturnMap = new ReturnMapDefinition();
    newReturnMap.setReturnMapCode(request.getReturnMapCode());
    newReturnMap.setIsActive(request.getIsActive());
    newReturnMap.setCreatedBy(request.getCreateUser());

    ReturnMapDefinition savedData = returnMapDefinitionRepository.save(newReturnMap);

    ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedData);

    if (dto == null) {
        throw new DataConflictException(ResultConstant.RECORD_ALREADY_EXISTS.getMessage());
    }

    return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), dto, 200);
}
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

    // Asserting the exception message
    assertEquals(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), thrown.getMessage());
}

 @Override
    public DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(CreateReturnMapDefinitionRequest request) throws DataConflictException {

        Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent()) {
            throw new DataConflictException(Long.valueOf(ResultConstant.RECORD_ALREADY_EXISTS.getMessage()));
        }

        ReturnMapDefinition newReturnMap = new ReturnMapDefinition();
        newReturnMap.setReturnMapCode(request.getReturnMapCode());
        newReturnMap.setIsActive(request.getIsActive());
        newReturnMap.setCreatedBy(request.getCreateUser());

        ReturnMapDefinition savedData = returnMapDefinitionRepository.save(newReturnMap);

        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedData);

        if (dto == null) {
            throw new DataConflictException(Long.valueOf(ResultConstant.RECORD_ALREADY_EXISTS.getMessage()));
        }

        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), dto,200);
    }




  @Test
    void testCreateReturnMapDefinition_RecordAlreadyExists() throws DataConflictException {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        request.setReturnMapCode("existingCode");

        when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.of(new ReturnMapDefinition()));

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.createReturnMapDefinition(request);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
    }



ttestinde bu hatayı alıyorum "java.lang.NumberFormatException: For input string: "Böyle bir kayıt zaten var"
"

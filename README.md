 @Test
    public void testUpdateReturnMap_Success() throws DataNotFoundException, DataConflictException {
        // Arrange
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");
        request.setInstitutionReturnText("Text");
        request.setBankReturnCode("456");
        request.setBankReturnText("Bank Text");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");
        request.setUpdateUser("User");

        // Mock ReturnMapDefinitionService to return a successful result
        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        returnMapDefinition.setReturnMapCode("MapCode");
        DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        // Mock ReturnMap data
        ReturnMap returnMap = new ReturnMap();
        returnMap.setId(1L);
        DataResult<ReturnMap> returnMapDataResult = new SuccessDataResult<>(returnMap);
        Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

        // Mock save operation
        Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

        // Mock mapping to DTO
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        // Act
        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        // Assert
        assertTrue(result.isSuccess());
        assertEquals(200, result.getCode());
        assertNotNull(result.getData());
    }

    @Test
    public void testUpdateReturnMap_DefinitionNotFound() throws DataNotFoundException, DataConflictException {
        // Arrange
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);

        // Mock ReturnMapDefinitionService to return an error result
        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error", null, 400);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        // Act & Assert
        // Expecting DataNotFoundException to be thrown
        DataNotFoundException exception = Assertions.assertThrows(DataNotFoundException.class, () -> {
            returnMapService.updateReturnMap(request);
        });

        // Verify ExceptionData
        ExceptionData error = exception.getError();
        assertNotNull(error);
        assertEquals(400L, error.getErrorCode());
        assertEquals("ReturnMapService", error.getApplicationName());
        assertEquals(ResultConstant.RECORD_NOT_FOUND.getMessage(), error.getErrorMessage());
    }

    @Test
    public void testUpdateReturnMap_Conflict() throws DataNotFoundException, DataConflictException {
        // Arrange
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(2L);  // Simulate conflict with different ID
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");

        // Mock ReturnMapDefinitionService to return success
        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        // Mock existing return map with a different ID to trigger conflict
        ReturnMap existingReturnMap = new ReturnMap();
        existingReturnMap.setId(1L); // Different ID from request, simulating conflict
        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.of(existingReturnMap));

        // Act & Assert
        // Expecting DataConflictException to be thrown
        DataConflictException exception = Assertions.assertThrows(DataConflictException.class, () -> {
            returnMapService.updateReturnMap(request);
        });

        // Verify ExceptionData fields
        ExceptionData error = exception.getError();
        assertNotNull(error);
        assertEquals(409L, error.getErrorCode());
        assertEquals("ReturnMapService", error.getApplicationName());
        assertEquals(ResultConstant.RECORD_NOT_FOUND.getMessage(), error.getErrorMessage());
    }

    @Test
    public void testUpdateReturnMap_NoExistingConflict() throws DataNotFoundException, DataConflictException {
        // Arrange
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");

        // Mock ReturnMapDefinitionService to return success
        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        DataResult<ReturnMapDefinition> definitionDataResult = new SuccessDataResult<>(returnMapDefinition);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        // Mock repository to return an empty result (no conflict)
        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.empty());

        // Mock existing ReturnMap data
        ReturnMap returnMap = new ReturnMap();
        returnMap.setId(1L);
        DataResult<ReturnMap> returnMapDataResult = new SuccessDataResult<>(returnMap);
        Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

        // Mock save operation
        Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

        // Mock mapping to DTO
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        // Act
        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        // Assert
        assertTrue(result.isSuccess());
        assertEquals(200, result.getCode());
        assertNotNull(result.getData());
    }

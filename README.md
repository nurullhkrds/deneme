  @Test
    public void testGetAllReturnMapDefinitionReturnMapWithIsActiveTrue_ReturnsActiveDefinitions() {
        // Mock data
        List<ReturnMapDefinition> definitionList = Arrays.asList(new ReturnMapDefinition());
        List<ReturnMapDefinitionDTO> dtoList = Arrays.asList(new ReturnMapDefinitionDTO());

        // Mocking repository and mapper behavior
        when(returnMapDefinitionRepository.findAllByIsActive(true)).thenReturn(definitionList);
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTOList(definitionList)).thenReturn(dtoList);

        // Call the service method
        DataResult<List<ReturnMapDefinitionDTO>> result = returnMapDefinitionService.getAllReturnMapDefinitionReturnMapWithIsActiveTrue();

        // Assertions
        assertNotNull(result);
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), result.getMessage());
        assertEquals(dtoList, result.getData());
        assertEquals(200, result.getStatus());

        // Verify interactions with mocks
        verify(returnMapDefinitionRepository, times(1)).findAllByIsActive(true);
        verify(returnMapDefinitionMapper, times(1)).toReturnMapDefinitionDTOList(definitionList);
    }

    @Test
    public void testGetAllReturnMapDefinitionReturnMapWithIsActiveTrue_ReturnsEmptyListWhenNoData() {
        // Mock empty data
        List<ReturnMapDefinition> definitionList = Collections.emptyList();
        List<ReturnMapDefinitionDTO> dtoList = Collections.emptyList();

        // Mocking repository and mapper behavior
        when(returnMapDefinitionRepository.findAllByIsActive(true)).thenReturn(definitionList);
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTOList(definitionList)).thenReturn(dtoList);

        // Call the service method
        DataResult<List<ReturnMapDefinitionDTO>> result = returnMapDefinitionService.getAllReturnMapDefinitionReturnMapWithIsActiveTrue();

        // Assertions
        assertNotNull(result);
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), result.getMessage());
        assertTrue(result.getData().isEmpty());
        assertEquals(200, result.getStatus());

        // Verify interactions with mocks
        verify(returnMapDefinitionRepository, times(1)).findAllByIsActive(true);
        verify(returnMapDefinitionMapper, times(1)).toReturnMapDefinitionDTOList(definitionList);
    }

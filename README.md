    @Test
    void testGetReturnMapDefinitionById_RecordExists() {
        Long id = 1L;
        ReturnMapDefinition mockEntity = new ReturnMapDefinition();
        ReturnMapDefinitionDTO mockDto = new ReturnMapDefinitionDTO();

        when(returnMapDefinitionRepository.findById(id)).thenReturn(Optional.of(mockEntity));
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTO(mockEntity)).thenReturn(mockDto);

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionById(id);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testGetReturnMapDefinitionById_RecordNotExists() {
        Long id = 1L;

        when(returnMapDefinitionRepository.findById(id)).thenReturn(Optional.empty());

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionById(id);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
        assertNull(result.getData());
    }

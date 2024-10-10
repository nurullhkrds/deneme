        List<String> institutions = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode).getData().getInstitutions();

        List<ReturnMapDTO> returnMapDTOList = returnMapList.stream()
                .map(entity -> {
                    ReturnMapDTO dto = returnMapMapper.toReturnMapDTO(entity);
                    if (dto != null && dto.getReturnMapDefinition() != null) {
                        dto.getReturnMapDefinition().setInstitutions(institutions);
                    }
                    return dto;
                })
                .collect(Collectors.toList());
                

bu satır eklendi 



  @Test
    void testSearchReturnMaps_WithAllCriteria() throws MicroException {
        String returnMapCode = "MAP123";
        String bankReturnCode = "BANK123";
        String institutionReturnCode = "INST123";

        List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
        List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

        when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
        when(returnMapMapper.toReturnMapDTOList(mockReturnMapList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDtoList, result.getData());

        verify(returnMapRepository, times(1)).findAll(any(Specification.class));
        verify(returnMapMapper, times(1)).toReturnMapDTOList(mockReturnMapList);
    }

    @Test
    void testSearchReturnMaps_NoResults() throws MicroException {
        String returnMapCode = "MAP123";
        String bankReturnCode = "BANK123";
        String institutionReturnCode = "INST123";

        List<ReturnMap> mockReturnMapList = Collections.emptyList();
        List<ReturnMapDTO> mockDtoList = Collections.emptyList();

        when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
        when(returnMapMapper.toReturnMapDTOList(mockReturnMapList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

        assertFalse(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertTrue(result.getData().isEmpty());

        verify(returnMapRepository, times(1)).findAll(any(Specification.class));
        verify(returnMapMapper, times(1)).toReturnMapDTOList(mockReturnMapList);
    }

    @Test
    void testSearchReturnMaps_WithNullCriteria() throws MicroException {
        String returnMapCode = null;
        String bankReturnCode = null;
        String institutionReturnCode = null;

        List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
        List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

        when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
        when(returnMapMapper.toReturnMapDTOList(mockReturnMapList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDtoList, result.getData());

        verify(returnMapRepository, times(1)).findAll(any(Specification.class));
        verify(returnMapMapper, times(1)).toReturnMapDTOList(mockReturnMapList);
    }

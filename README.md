  @Test
    void testGetReturnMapById_RecordExists() {
        Long returnMapCodeId = 1L;
        ReturnMap mockReturnMap = new ReturnMap();
        ReturnMapDTO mockDto = new ReturnMapDTO();

        when(returnMapRepository.findById(returnMapCodeId)).thenReturn(Optional.of(mockReturnMap));
        when(returnMapMapper.toReturnMapDTO(mockReturnMap)).thenReturn(mockDto);

        DataResult<ReturnMapDTO> result = returnMapService.getReturnMapById(returnMapCodeId);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testGetReturnMapById_RecordNotFound() {
        Long returnMapCodeId = 1L;

        when(returnMapRepository.findById(returnMapCodeId)).thenReturn(Optional.empty());

        DataResult<ReturnMapDTO> result = returnMapService.getReturnMapById(returnMapCodeId);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
        assertNull(result.getData());
    }

    @Test
    void testSearchReturnMaps_WithAllCriteria() {
        String returnMapCode = "MAP123";
        String bankReturnCode = "BANK123";
        String institutionReturnCode = "INST123";
        
        List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
        List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

        Specification<ReturnMap> spec = mock(Specification.class);
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
    void testSearchReturnMaps_NoResults() {
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
    void testSearchReturnMaps_WithNullCriteria() {
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

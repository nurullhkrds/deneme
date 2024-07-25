   @Test
    void testGetReturnMapById_Success() {
        Long returnMapCodeId = 1L;
        ReturnMap returnMap = new ReturnMap();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();

        when(returnMapRepository.findById(returnMapCodeId)).thenReturn(Optional.of(returnMap));
        when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.getReturnMapById(returnMapCodeId);

        assertTrue(result.isSuccess());
        assertEquals("result found", result.getMessage());
        assertEquals(returnMapDTO, result.getData());
        assertEquals(200, result.getStatusCode());
    }

    @Test
    void testGetReturnMapById_NotFound() {
        Long returnMapCodeId = 1L;

        when(returnMapRepository.findById(returnMapCodeId)).thenReturn(Optional.empty());

        DataResult<ReturnMapDTO> result = returnMapService.getReturnMapById(returnMapCodeId);

        assertFalse(result.isSuccess());
        assertEquals("result not found !", result.getMessage());
        assertNull(result.getData());
        assertEquals(400, result.getStatusCode());
    }

    @Test
    void testSearchReturnMaps() {
        String returnMapCode = "code";
        String bankReturnCode = "bankCode";
        String institutionReturnCode = "instCode";

        ReturnMap returnMap1 = new ReturnMap();
        ReturnMap returnMap2 = new ReturnMap();
        List<ReturnMap> returnMapList = Arrays.asList(returnMap1, returnMap2);
        ReturnMapDTO returnMapDTO1 = new ReturnMapDTO();
        ReturnMapDTO returnMapDTO2 = new ReturnMapDTO();
        List<ReturnMapDTO> returnMapDTOList = Arrays.asList(returnMapDTO1, returnMapDTO2);

        when(returnMapRepository.findAll(any(Specification.class))).thenReturn(returnMapList);
        when(returnMapMapper.toReturnMapDTOList(returnMapList)).thenReturn(returnMapDTOList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

        assertTrue(result.isSuccess());
        assertEquals("Result listed", result.getMessage());
        assertEquals(returnMapDTOList, result.getData());
        assertEquals(200, result.getStatusCode());
    }

    @Test
    void testSearchReturnMaps_Empty() {
        when(returnMapRepository.findAll(any(Specification.class))).thenReturn(Arrays.asList());
        when(returnMapMapper.toReturnMapDTOList(anyList())).thenReturn(Arrays.asList());

        DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(null, null, null);

        assertFalse(result.isSuccess());
        assertEquals("Listed is empty", result.getMessage());
        assertTrue(result.getData().isEmpty());
        assertEquals(200, result.getStatusCode());
    }

    @Test
    void testGetAllByReturnMapCode_Success() {
        String returnMapCode = "code";
        ReturnMap returnMap1 = new ReturnMap();
        ReturnMap returnMap2 = new ReturnMap();
        List<ReturnMap> returnMapList = Arrays.asList(returnMap1, returnMap2);
        ReturnMapDTO returnMapDTO1 = new ReturnMapDTO();
        ReturnMapDTO returnMapDTO2 = new ReturnMapDTO();
        List<ReturnMapDTO> returnMapDTOList = Arrays.asList(returnMapDTO1, returnMapDTO2);

        when(returnMapRepository.findByReturnMapCode(returnMapCode)).thenReturn(returnMapList);
        when(returnMapMapper.toReturnMapDTOList(returnMapList)).thenReturn(returnMapDTOList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllByReturnMapCode(returnMapCode);

        assertTrue(result.isSuccess());
        assertEquals("Data retrieved successfully", result.getMessage());
        assertEquals(returnMapDTOList, result.getData());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
    }

    @Test
    void testGetAllByReturnMapCode_NotFound() {
        String returnMapCode = "code";

        when(returnMapRepository.findByReturnMapCode(returnMapCode)).thenReturn(Arrays.asList());

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllByReturnMapCode(returnMapCode);

        assertFalse(result.isSuccess());
        assertEquals("No data found", result.getMessage());
        assertTrue(result.getData().isEmpty());
        assertEquals(HttpStatus.NOT_FOUND.value(), result.getStatusCode());
    }

// 1. Test: Tüm kriterlerle ve institutions null değil
@Test
void testSearchReturnMaps_WithAllCriteriaAndInstitutions() throws MicroException {
    String returnMapCode = "MAP123";
    String bankReturnCode = "BANK123";
    String institutionReturnCode = "INST123";

    List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());

    // Mock ReturnMapDefinitionDTO ve ReturnMapDTO
    ReturnMapDefinitionDTO mockReturnMapDefinitionDTO = new ReturnMapDefinitionDTO(1L, returnMapCode, true, Arrays.asList("Institution1", "Institution2"));
    ReturnMapDTO mockReturnMapDTO = new ReturnMapDTO(1L, mockReturnMapDefinitionDTO, returnMapCode, "INST123", "Test Text", "BANK123", "Test Text", "ERROR", true);

    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockReturnMapDTO);
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
            .thenReturn(new SuccessDataResult<>("", mockReturnMapDefinitionDTO, 200));

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(1, result.getData().size());
    assertEquals(mockReturnMapDTO, result.getData().get(0));
    assertEquals(mockReturnMapDefinitionDTO.getInstitutions(), result.getData().get(0).getReturnMapDefinition().getInstitutions());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
    verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionByReturnMapCode(returnMapCode);
}

// 2. Test: Institutions null olduğunda
@Test
void testSearchReturnMaps_WithNullInstitutions() throws MicroException {
    String returnMapCode = "MAP123";
    String bankReturnCode = "BANK123";
    String institutionReturnCode = "INST123";

    List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());

    // Institutions null olan ReturnMapDefinitionDTO ve ReturnMapDTO
    ReturnMapDefinitionDTO mockReturnMapDefinitionDTO = new ReturnMapDefinitionDTO(1L, returnMapCode, true, null);
    ReturnMapDTO mockReturnMapDTO = new ReturnMapDTO(1L, mockReturnMapDefinitionDTO, returnMapCode, "INST123", "Test Text", "BANK123", "Test Text", "ERROR", true);

    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockReturnMapDTO);
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
            .thenReturn(new SuccessDataResult<>("", mockReturnMapDefinitionDTO, 200));

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(1, result.getData().size());
    assertNull(result.getData().get(0).getReturnMapDefinition().getInstitutions());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
    verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionByReturnMapCode(returnMapCode);
}

// 3. Test: ReturnMapDefinition null olduğunda
@Test
void testSearchReturnMaps_WithNullReturnMapDefinition() throws MicroException {
    String returnMapCode = "MAP123";
    String bankReturnCode = "BANK123";
    String institutionReturnCode = "INST123";

    List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());

    // ReturnMapDefinitionDTO null olan ReturnMapDTO
    ReturnMapDTO mockReturnMapDTO = new ReturnMapDTO(1L, null, returnMapCode, "INST123", "Test Text", "BANK123", "Test Text", "ERROR", true);

    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockReturnMapDTO);
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
            .thenReturn(null);

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(1, result.getData().size());
    assertNull(result.getData().get(0).getReturnMapDefinition());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
    verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionByReturnMapCode(returnMapCode);
}

// 1. Test: Tüm kriterlerle ve institutions null değil
@Test
void testSearchReturnMaps_WithAllCriteriaAndInstitutions() throws MicroException {
    String returnMapCode = "MAP123";
    String bankReturnCode = "BANK123";
    String institutionReturnCode = "INST123";

    List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
    List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

    // Mock institutions
    List<String> mockInstitutions = Arrays.asList("Institution1", "Institution2");

    // Mock repository, mapper and returnMapDefinitionService calls
    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockDtoList.get(0));

    // Mock returnMapDefinitionService
    ReturnMapDefinitionDTO mockReturnMapDefinitionDTO = new ReturnMapDefinitionDTO(1L, returnMapCode, true, mockInstitutions);
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
        .thenReturn(new SuccessDataResult<>(mockReturnMapDefinitionDTO));

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(mockDtoList, result.getData());
    assertEquals(mockInstitutions, result.getData().get(0).getReturnMapDefinition().getInstitutions());

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
    List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

    // Mock repository and mapper calls
    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockDtoList.get(0));

    // Mock returnMapDefinitionService to return null institutions
    ReturnMapDefinitionDTO mockReturnMapDefinitionDTO = new ReturnMapDefinitionDTO(1L, returnMapCode, true, null);
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
        .thenReturn(new SuccessDataResult<>(mockReturnMapDefinitionDTO));

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(mockDtoList, result.getData());
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
    List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

    // Mock repository and mapper calls
    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockDtoList.get(0));

    // Mock returnMapDefinitionService to return null result
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
        .thenReturn(null);

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(mockDtoList, result.getData());
    assertNull(result.getData().get(0).getReturnMapDefinition().getInstitutions());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
    verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionByReturnMapCode(returnMapCode);
}

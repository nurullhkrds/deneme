// 1. Test: Tüm kriterlerle
@Test
void testSearchReturnMaps_WithAllCriteria() throws MicroException {
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

// 2. Test: Sonuç yoksa
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
    verify(returnMapDefinitionService, times(0)).getReturnMapDefinitionByReturnMapCode(anyString());
}

// 3. Test: Kriterler null ise
@Test
void testSearchReturnMaps_WithNullCriteria() throws MicroException {
    String returnMapCode = null;
    String bankReturnCode = null;
    String institutionReturnCode = null;

    List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
    List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

    when(returnMapRepository.findAll(any(Specification.class))).thenReturn(mockReturnMapList);
    when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(mockDtoList.get(0));

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(mockDtoList, result.getData());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
    verify(returnMapDefinitionService, times(0)).getReturnMapDefinitionByReturnMapCode(anyString()); // Çünkü returnMapCode null
}

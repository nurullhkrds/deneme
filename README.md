  List<String> institutions;

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);
        if (result != null && result.getData() != null) {
            institutions = result.getData().getInstitutions();
        } else {
            institutions = null;
        }

        List<ReturnMapDTO> returnMapDTOList = returnMapList.stream()
                .map(entity -> {
                    ReturnMapDTO dto = returnMapMapper.toReturnMapDTO(entity);
                    if (dto != null && dto.getReturnMapDefinition() != null) {
                        dto.getReturnMapDefinition().setInstitutions(institutions);
                    }
                    return dto;
                })
                .collect(Collectors.toCollection(ArrayList::new));


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

    // Mock returnMapDefinitionService to return null data
    when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode))
        .thenReturn(null);

    DataResult<List<ReturnMapDTO>> result = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);

    assertTrue(result.isSuccess());
    assertEquals(200, result.getStatusCode());
    assertEquals(mockDtoList, result.getData());
    assertNull(result.getData().get(0).getReturnMapDefinition().getInstitutions());

    verify(returnMapRepository, times(1)).findAll(any(Specification.class));
    verify(returnMapMapper, times(1)).toReturnMapDTO(any(ReturnMap.class));
}

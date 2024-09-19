@Test
void testGetReturnMapDefinitionById_RecordNotExists() throws MicroException {
    Long id = 1L;

    when(returnMapDefinitionRepository.findById(id)).thenReturn(Optional.empty());

    assertThrows(DataNotFoundException.class, () -> {
        returnMapDefinitionService.getReturnMapDefinitionById(id);
    });
}
@Test
void testGetReturnMapDefinitionByReturnMapCode_RecordNotExists() throws MicroException {
    String returnMapCode = "testCode";

    when(returnMapDefinitionRepository.findByReturnMapCode(returnMapCode)).thenReturn(Optional.empty());

    assertThrows(DataNotFoundException.class, () -> {
        returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);
    });
}

@Test
void testGetProvisionService() {
    // Mock ProvisionService instance
    ProvisionService mockProvisionService = Mockito.mock(ProvisionService.class);
    EnumProvisionType validProvisionType = EnumProvisionType.CARD;

    when(provisionServiceMap.get(validProvisionType)).thenReturn(mockProvisionService);

    ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

    assertNotNull(result);
    assertEquals(mockProvisionService, result);
}

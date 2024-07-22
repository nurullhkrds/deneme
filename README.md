@Test
void whenGetProvisionServiceWithValidType_thenShouldReturnService() {
    // Arrange
    ProvisionService mockProvisionService = mock(ProvisionService.class);
    EnumProvisionType validProvisionType = EnumProvisionType.CARD;

    List<ProvisionService> mockServices = List.of(mockProvisionService);
    provisionServices = mock(List.class);

    when(provisionServices.stream()).thenReturn(mockServices.stream());
    when(mockProvisionService.getProvisionType()).thenReturn(validProvisionType);

    // Act
    ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

    // Assert
    assertNotNull(result);
    assertEquals(mockProvisionService, result);
}

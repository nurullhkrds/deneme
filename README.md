 @Test
    public void shouldReturnProvisionServiceWhenValidProvisionType() {
        // Arrange
        EnumProvisionType validType = EnumProvisionType.CARD;
        when(mockProvisionService.getProvisionType()).thenReturn(validType);
        when(mockProvisionServiceList.stream()).thenReturn(Collections.singletonList(mockProvisionService).stream());

        // Act
        ProvisionService result = provisionFactory.getProvisionService(validType);

        // Assert
        assertEquals(mockProvisionService, result);
    }

    @Test
    public void shouldThrowIllegalArgumentExceptionWhenInvalidProvisionType() {
        // Arrange
        EnumProvisionType invalidType = EnumProvisionType.INVALID_TYPE;
        when(mockProvisionServiceList.stream()).thenReturn(Collections.singletonList(mockProvisionService).stream());
        when(mockProvisionService.getProvisionType()).thenReturn(EnumProvisionType.CARD); // Farklı bir EnumProvisionType döndürmek için

        // Act & Assert
        IllegalArgumentException exception = assertThrows(IllegalArgumentException.class,
                () -> provisionFactory.getProvisionService(invalidType));

        assertEquals("No implementation found for provision type: " + invalidType, exception.getMessage());
    }

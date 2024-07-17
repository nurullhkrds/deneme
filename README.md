 @Test
    void shouldReturnProvisionServiceWhenValidProvisionType() {
        // Mock ProvisionService instance
        ProvisionService mockProvisionService = Mockito.mock(ProvisionService.class);
        EnumProvisionType validProvisionType = EnumProvisionType.TYPE1;

        // Setup mock behavior
        when(provisionServices.stream()).thenReturn(List.of(mockProvisionService).stream());
        when(mockProvisionService.getProvisionType()).thenReturn(validProvisionType);

        // Test
        ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

        // Assert
        assertNotNull(result);
        assertEquals(mockProvisionService, result);
    }

    @Test
    void shouldThrowIllegalArgumentExceptionWhenInvalidProvisionType() {
        // Mock ProvisionService instance
        ProvisionService mockProvisionService = Mockito.mock(ProvisionService.class);
        EnumProvisionType validProvisionType = EnumProvisionType.TYPE1;

        // Setup mock behavior
        when(provisionServices.stream()).thenReturn(List.of(mockProvisionService).stream());
        when(mockProvisionService.getProvisionType()).thenReturn(validProvisionType);

        // Test
        EnumProvisionType invalidProvisionType = EnumProvisionType.INVALID_TYPE;
        IllegalArgumentException exception = assertThrows(IllegalArgumentException.class, () -> provisionFactory.getProvisionService(invalidProvisionType));

        // Assert
        assertEquals("No implementation found for provision type: " + invalidProvisionType, exception.getMessage());
    }

 @Test
    public void testDoReverseAccounting_Success() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        MakeReverseProvisionResponse mockResponse = new MakeReverseProvisionResponse();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenReturn(mockResponse);

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertTrue(result.isSuccess());
        assertNull(result.getError());
    }

    @Test
    public void testDoReverseAccounting_ServiceCallException() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenThrow(new RuntimeException("Service call failed"));

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    @Test
    public void testDoReverseAccounting_GeneralException() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenThrow(new RuntimeException("Unexpected error"));

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    private CreateReverseAccountingDTO createSampleDTO() {
        CreateReverseAccountingDTO dto = new CreateReverseAccountingDTO();
        dto.setChannelTransactionId("1234567890");
        dto.setContractNo(123456789L); // Example long value
        // Set other necessary properties
        return dto;
    }

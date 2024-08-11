@Test
    public void testGetProcessChannel_ValidInputs() {
        // Arrange
        String code = "testCode";
        String channelCode = "testChannel";
        ProcessChannelDTO expectedChannel = new ProcessChannelDTO();
        
        when(processChannelService.findProcessChannel(eq(code), eq(channelCode))).thenReturn(expectedChannel);

        // Act
        ProcessChannelDTO result = processService.getProcessChannel(code, channelCode);

        // Assert
        assertNotNull(result);
        assertEquals(expectedChannel, result);
    }

    @Test
    public void testGetProcessChannel_InvalidInputs() {
        // Act
        ProcessChannelDTO result = processService.getProcessChannel("", "");

        // Assert
        assertNull(result);
    }

    @Test
    public void testGetInstitutionDebtTypeForProcess_WithDebtTypeId() {
        // Arrange
        Long debtTypeId = 1L;
        InstitutionDebtTypeDTO expectedDebtType = new InstitutionDebtTypeDTO();
        
        when(institutionDebtTypeService.getDebtType(eq(debtTypeId))).thenReturn(expectedDebtType);

        // Act
        InstitutionDebtTypeDTO result = processService.getInstitutionDebtTypeForProcess(null, null, debtTypeId);

        // Assert
        assertNotNull(result);
        assertEquals(expectedDebtType, result);
    }

    @Test
    public void testGetInstitutionDebtTypeForProcess_WithoutDebtTypeId() {
        // Arrange
        String productCode = "productCode";
        String institutionCode = "institutionCode";
        InstitutionDebtTypeDTO expectedDebtType = new InstitutionDebtTypeDTO();
        
        when(institutionDebtTypeService.getDefaultDebtType(eq(productCode), eq(institutionCode))).thenReturn(expectedDebtType);

        // Act
        InstitutionDebtTypeDTO result = processService.getInstitutionDebtTypeForProcess(productCode, institutionCode, null);

        // Assert
        assertNotNull(result);
        assertEquals(expectedDebtType, result);
    }

    @Test
    public void testGetInstitutionDebtTypeForProcess_InvalidInputs() {
        // Act
        InstitutionDebtTypeDTO result = processService.getInstitutionDebtTypeForProcess("", "", null);

        // Assert
        assertNull(result);
    }

@Test
    public void testGetAllReturnMapDefinitionReturnMapWithIsActiveTrue_ReturnsData() throws Exception {
        // Mock data
        List<ReturnMapDefinitionDTO> dtoList = Arrays.asList(new ReturnMapDefinitionDTO());
        DataResult<List<ReturnMapDefinitionDTO>> mockResult = new SuccessDataResult<>(ResultConstant.DATA_LISTED.getMessage(), dtoList, HttpStatus.OK.value());

        // Mock service behavior
        when(returnMapDefinitionService.getAllReturnMapDefinitionReturnMapWithIsActiveTrue()).thenReturn(mockResult);

        // Perform GET request
        mockMvc.perform(get("/returnMapDefinitions/getAllReturnMapDefinitionReturnMapWithIsActiveTrue")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.message").value(ResultConstant.DATA_LISTED.getMessage()))
                .andExpect(jsonPath("$.data").isArray())
                .andExpect(jsonPath("$.data[0]").exists())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        // Verify service method is called
        verify(returnMapDefinitionService, times(1)).getAllReturnMapDefinitionReturnMapWithIsActiveTrue();
    }

    @Test
    public void testGetAllReturnMapDefinitionReturnMapWithIsActiveTrue_ReturnsEmptyList() throws Exception {
        // Mock data
        DataResult<List<ReturnMapDefinitionDTO>> mockResult = new SuccessDataResult<>(ResultConstant.DATA_LISTED.getMessage(), Arrays.asList(), HttpStatus.OK.value());

        // Mock service behavior
        when(returnMapDefinitionService.getAllReturnMapDefinitionReturnMapWithIsActiveTrue()).thenReturn(mockResult);

        // Perform GET request
        mockMvc.perform(get("/returnMapDefinitions/getAllReturnMapDefinitionReturnMapWithIsActiveTrue")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.message").value(ResultConstant.DATA_LISTED.getMessage()))
                .andExpect(jsonPath("$.data").isArray())
                .andExpect(jsonPath("$.data").isEmpty())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        // Verify service method is called
        verify(returnMapDefinitionService, times(1)).getAllReturnMapDefinitionReturnMapWithIsActiveTrue();
    }

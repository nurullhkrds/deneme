    @Test
    public void testCreateReturnMap() throws Exception {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Created", returnMapDTO, HttpStatus.CREATED.value());

        when(returnMapService.createReturnMap(any(CreateReturnMapRequest.class))).thenReturn(dataResult);

        mockMvc.perform(post("/returnMaps/createReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{ /* JSON representation of CreateReturnMapRequest */ }"))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.CREATED.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Created"));

        verify(returnMapService, times(1)).createReturnMap(any(CreateReturnMapRequest.class));
    }

    @Test
    public void testUpdateReturnMap() throws Exception {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Updated", returnMapDTO, HttpStatus.OK.value());

        when(returnMapService.updateReturnMap(any(UpdateReturnMapRequest.class))).thenReturn(dataResult);

        mockMvc.perform(put("/returnMaps/updateReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{ /* JSON representation of UpdateReturnMapRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Updated"));

        verify(returnMapService, times(1)).updateReturnMap(any(UpdateReturnMapRequest.class));
    }
    @Test
    public void testCopyReturnMaps() throws Exception {
        CopyForIdsAndDataRequest request = new CopyForIdsAndDataRequest();
        Result result = new Result(true, "Copied", HttpStatus.OK.value());

        when(returnMapService.copyReturnMaps(any(CopyForIdsAndDataRequest.class))).thenReturn(result);

        mockMvc.perform(post("/returnMaps/copy")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{ /* JSON representation of CopyForIdsAndDataRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Copied"));

        verify(returnMapService, times(1)).copyReturnMaps(any(CopyForIdsAndDataRequest.class));
    }

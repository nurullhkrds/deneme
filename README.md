    @Test
    public void testDeleteReturnMaps() throws Exception {
        DeleteIdsRequest request = new DeleteIdsRequest();
        Result result = new Result(true, "Deleted", HttpStatus.OK.value());

        when(returnMapService.deleteReturnMaps(any(DeleteIdsRequest.class))).thenReturn(result);

        mockMvc.perform(delete("/returnMaps/delete")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content("{ /* JSON representation of DeleteIdsRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Deleted"));

        verify(returnMapService, times(1)).deleteReturnMaps(any(DeleteIdsRequest.class));
    }

java.lang.AssertionError: Status expected:<200> but was:<400>
Expected :200
Actual   :400
<Click to see difference>

@Test
public void testDeleteReturnMaps() throws Exception {
    DeleteIdsRequest request = new DeleteIdsRequest();
    request.setIds(Arrays.asList(1L, 2L, 3L)); // Example IDs
    Result result = new Result(true, "Deleted", HttpStatus.OK.value());

    when(returnMapService.deleteReturnMaps(any(DeleteIdsRequest.class))).thenReturn(result);

    ObjectMapper objectMapper = new ObjectMapper();
    String jsonRequest = objectMapper.writeValueAsString(request);

    mockMvc.perform(delete("/returnMaps/delete")
                    .contentType(MediaType.APPLICATION_JSON)
                    .content(jsonRequest))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
            .andExpect(jsonPath("$.success").value(true))
            .andExpect(jsonPath("$.message").value("Deleted"));

    verify(returnMapService, times(1)).deleteReturnMaps(any(DeleteIdsRequest.class));
}

public class ReturnMapControllerTest {

    private MockMvc mockMvc;

    @Mock
    private IReturnMapService returnMapService;

    @Mock
    private RequestContext requestContext;

    @InjectMocks
    private ReturnMapController returnMapController;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        mockMvc = MockMvcBuilders.standaloneSetup(returnMapController).build();
    }

    @Test
    public void testGetAllByReturnMapCode() throws Exception {
        List<ReturnMapDTO> returnMapDTOList = Arrays.asList(new ReturnMapDTO());
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(true, "Success", returnMapDTOList, HttpStatus.OK.value());
        
        when(returnMapService.getAllByReturnMapCode(anyString())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getAllByReturnMapCode")
                .param("returnMapCode", "code"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Success"));

        verify(returnMapService, times(1)).getAllByReturnMapCode(anyString());
    }

    @Test
    public void testGetAll() throws Exception {
        List<ReturnMapDTO> returnMapDTOList = Arrays.asList(new ReturnMapDTO());
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(true, "Success", returnMapDTOList, HttpStatus.OK.value());

        when(returnMapService.getAll()).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getAll"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Success"));

        verify(returnMapService, times(1)).getAll();
    }

    @Test
    public void testGetReturnMapById() throws Exception {
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Success", returnMapDTO, HttpStatus.OK.value());

        when(returnMapService.getReturnMapById(anyLong())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getReturnMapById")
                .param("returnMapCodeId", "1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Success"));

        verify(returnMapService, times(1)).getReturnMapById(anyLong());
    }

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
    public void testSearchReturnMaps() throws Exception {
        List<ReturnMapDTO> returnMapDTOList = Arrays.asList(new ReturnMapDTO());
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(true, "Success", returnMapDTOList, HttpStatus.OK.value());

        when(returnMapService.searchReturnMaps(anyString(), anyString(), anyString())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/search")
                .param("returnMapCode", "code")
                .param("bankReturnCode", "bankCode")
                .param("institutionReturnCode", "instCode"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Success"));

        verify(returnMapService, times(1)).searchReturnMaps(anyString(), anyString(), anyString());
    }

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
}

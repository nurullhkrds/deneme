@Tag(name = "Management ReturnMap BFF Controller")
@RequestMapping(RETURN_MAP_PATH)
@RestController
public class ReturnMapController {

    private final AdapterReturnMapClient adapterReturnMapClient;
    private final IUserContext userContext;


  @Autowired
    public ReturnMapController(AdapterReturnMapClient adapterReturnMapClient, IUserContext userContext) {
        this.adapterReturnMapClient = adapterReturnMapClient;
        this.userContext = userContext;
    }
    
    @PostMapping("/createReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> createReturnMap(
            @RequestBody CreateReturnMapRequest request)
            throws MicroException {
        request.setCreateUser(userContext.getUserCode());
        DataResult<ReturnMapDTO> result = adapterReturnMapClient.createReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

    @PutMapping("/updateReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> updateReturnMap(
            @RequestBody UpdateReturnMapRequest request)
            throws MicroException {
        request.setUpdateUser(userContext.getUserCode());
        DataResult<ReturnMapDTO> result = adapterReturnMapClient.updateReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }





    @Test
     void testCreateReturnMap() throws Exception {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Created", returnMapDTO, HttpStatus.CREATED.value());

        when(returnMapService.createReturnMap(any(CreateReturnMapRequest.class))).thenReturn(dataResult);

        ObjectMapper objectMapper = new ObjectMapper();
        String jsonRequest = objectMapper.writeValueAsString(request);

        mockMvc.perform(post("/returnMaps/createReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(jsonRequest))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.CREATED.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Created"));

        verify(returnMapService, times(1)).createReturnMap(any(CreateReturnMapRequest.class));
    }

    @Test
     void testUpdateReturnMap() throws Exception {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Updated", returnMapDTO, HttpStatus.OK.value());

        when(returnMapService.updateReturnMap(any(UpdateReturnMapRequest.class))).thenReturn(dataResult);

        ObjectMapper objectMapper = new ObjectMapper();
        String jsonRequest = objectMapper.writeValueAsString(request);

        mockMvc.perform(put("/returnMaps/updateReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(jsonRequest))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Updated"));

        verify(returnMapService, times(1)).updateReturnMap(any(UpdateReturnMapRequest.class));
    }


org.springframework.web.util.NestedServletException: Request processing failed; nested exception is java.lang.NullPointerException: Cannot invoke "com.ykb.architecture.micro.microsecurity.entity.user.IUserContext.getUserCode()" because "this.userContext" is null

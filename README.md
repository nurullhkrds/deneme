@Tag(name = "Management ReturnMapDefinition BFF Controller")
@RequestMapping(RETURN_MAP_DEFINITION_PATH)
@RestController
public class ReturnMapDefinitionController {

    private final AdapterReturnMapDefinitionClient adapterReturnMapDefinitionClient;

    private final IUserContext userContext;


    public ReturnMapDefinitionController(AdapterReturnMapDefinitionClient adapterReturnMapDefinitionClient, IUserContext userContext) {
        this.adapterReturnMapDefinitionClient = adapterReturnMapDefinitionClient;
        this.userContext = userContext;
    }

    @PostMapping("/createReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> createReturnMapDefinition(
            @RequestBody CreateReturnMapDefinitionRequest request) throws InvalidRequestException {
        request.setCreateUser(userContext.getUserCode());
        DataResult<ReturnMapDefinitionDTO> result = adapterReturnMapDefinitionClient.createReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PutMapping("/updateReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> updateReturnMapDefinition(
            @RequestBody UpdateReturnMapDefinitionRequest request) {
        request.setUpdateUser(userContext.getUserCode());
        DataResult<ReturnMapDefinitionDTO> result = adapterReturnMapDefinitionClient.updateReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }






    @Test
    void testCreateReturnMapDefinition() throws Exception {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(true, "Success", returnMapDefinitionDTO, 200);

        when(returnMapDefinitionService.createReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(post("/returnMapDefinitions/createReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).createReturnMapDefinition(any());
    }

    @Test
    void testUpdateReturnMapDefinition() throws Exception {
        UpdateReturnMapDefinitionRequest request = new UpdateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(true, "Success", returnMapDefinitionDTO, 200);

        when(returnMapDefinitionService.updateReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(put("/returnMapDefinitions/updateReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).updateReturnMapDefinition(any());
    }


org.springframework.web.util.NestedServletException: Request processing failed; nested exception is java.lang.NullPointerException: Cannot invoke "com.ykb.architecture.micro.microsecurity.entity.user.IUserContext.getUserCode()" because "this.userContext" is null

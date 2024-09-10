import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

class ReturnMapDefinitionControllerTest {

    @Mock
    private AdapterReturnMapDefinitionClient adapterReturnMapDefinitionClient;

    @Mock
    private IUserContext userContext;

    @InjectMocks
    private ReturnMapDefinitionController returnMapDefinitionController;

    private MockMvc mockMvc;

    @BeforeEach
    void setUp() {
        mockMvc = MockMvcBuilders.standaloneSetup(returnMapDefinitionController).build();
    }

    @Test
    void testCreateReturnMapDefinition() throws Exception {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(true, "Success", returnMapDefinitionDTO, 200);

        // Mock userContext.getUserCode()
        when(userContext.getUserCode()).thenReturn("testUserCode");
        when(adapterReturnMapDefinitionClient.createReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(post("/returnMapDefinitions/createReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(adapterReturnMapDefinitionClient, times(1)).createReturnMapDefinition(any());
    }

    @Test
    void testUpdateReturnMapDefinition() throws Exception {
        UpdateReturnMapDefinitionRequest request = new UpdateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(true, "Success", returnMapDefinitionDTO, 200);

        // Mock userContext.getUserCode()
        when(userContext.getUserCode()).thenReturn("testUserCode");
        when(adapterReturnMapDefinitionClient.updateReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(put("/returnMapDefinitions/updateReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(adapterReturnMapDefinitionClient, times(1)).updateReturnMapDefinition(any());
    }
}

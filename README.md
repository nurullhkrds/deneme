import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import java.util.Arrays;
import java.util.List;

public class ReturnMapDefinitionControllerTest {

    private MockMvc mockMvc;

    @Mock
    private IReturnMapDefinitionService returnMapDefinitionService;

    @InjectMocks
    private ReturnMapDefinitionController returnMapDefinitionController;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        mockMvc = MockMvcBuilders.standaloneSetup(returnMapDefinitionController).build();
    }

    @Test
    void testGetAllReturnMapDefinitionReturnMap() throws Exception {
        List<ReturnMapDefinitionDTO> returnMapDefinitions = Arrays.asList(new ReturnMapDefinitionDTO());
        DataResult<List<ReturnMapDefinitionDTO>> dataResult = new DataResult<>(returnMapDefinitions, true, "Success", 200);

        when(returnMapDefinitionService.getAllReturnMapDefinitionReturnMap()).thenReturn(dataResult);

        mockMvc.perform(get("/returnMapDefinitions/getAllReturnMapDefinitionReturnMap"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isArray());
        
        verify(returnMapDefinitionService, times(1)).getAllReturnMapDefinitionReturnMap();
    }

    @Test
    void testGetReturnMapDefinitionByReturnMapCode() throws Exception {
        String returnMapCode = "exampleCode";
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(returnMapDefinitionDTO, true, "Success", 200);

        when(returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode)).thenReturn(dataResult);

        mockMvc.perform(get("/returnMapDefinitions/getReturnMapDefinitionByReturnMapCode")
                        .param("returnMapCode", returnMapCode))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionByReturnMapCode(returnMapCode);
    }

    @Test
    void testGetReturnMapDefinitionById() throws Exception {
        Long id = 1L;
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(returnMapDefinitionDTO, true, "Success", 200);

        when(returnMapDefinitionService.getReturnMapDefinitionById(id)).thenReturn(dataResult);

        mockMvc.perform(get("/returnMapDefinitions/getReturnMapDefinitionById")
                        .param("id", id.toString()))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).getReturnMapDefinitionById(id);
    }

    @Test
    void testCreateReturnMapDefinition() throws Exception {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(returnMapDefinitionDTO, true, "Success", 201);

        when(returnMapDefinitionService.createReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(post("/returnMapDefinitions/createReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).createReturnMapDefinition(any());
    }

    @Test
    void testUpdateReturnMapDefinition() throws Exception {
        UpdateReturnMapDefinitionRequest request = new UpdateReturnMapDefinitionRequest();
        ReturnMapDefinitionDTO returnMapDefinitionDTO = new ReturnMapDefinitionDTO();
        DataResult<ReturnMapDefinitionDTO> dataResult = new DataResult<>(returnMapDefinitionDTO, true, "Success", 200);

        when(returnMapDefinitionService.updateReturnMapDefinition(any())).thenReturn(dataResult);

        mockMvc.perform(put("/returnMapDefinitions/updateReturnMapDefinition")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.data").isNotEmpty());

        verify(returnMapDefinitionService, times(1)).updateReturnMapDefinition(any());
    }

    @Test
    void testDeleteReturnMapDefinitions() throws Exception {
        DeleteIdsRequest request = new DeleteIdsRequest();
        Result result = new Result(true, "Deleted successfully", 200);

        when(returnMapDefinitionService.deleteReturnMapDefinitions(any())).thenReturn(result);

        mockMvc.perform(delete("/returnMapDefinitions/delete")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(new ObjectMapper().writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Deleted successfully"));

        verify(returnMapDefinitionService, times(1)).deleteReturnMapDefinitions(any());
    }
}

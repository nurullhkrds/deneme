import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

public class ReturnMapControllerTest {

    @Mock
    private AdapterReturnMapClient adapterReturnMapClient;

    @Mock
    private IUserContext userContext;

    @InjectMocks
    private ReturnMapController returnMapController;

    private MockMvc mockMvc;

    @BeforeEach
    void setUp() {
        mockMvc = MockMvcBuilders.standaloneSetup(returnMapController).build();
    }

    @Test
    void testCreateReturnMap() throws Exception {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Created", returnMapDTO, HttpStatus.CREATED.value());

        // Mock IUserContext and AdapterReturnMapClient
        when(userContext.getUserCode()).thenReturn("testUserCode");
        when(adapterReturnMapClient.createReturnMap(any(CreateReturnMapRequest.class))).thenReturn(dataResult);

        ObjectMapper objectMapper = new ObjectMapper();
        String jsonRequest = objectMapper.writeValueAsString(request);

        mockMvc.perform(post("/returnMaps/createReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(jsonRequest))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.CREATED.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Created"));

        verify(adapterReturnMapClient, times(1)).createReturnMap(any(CreateReturnMapRequest.class));
    }

    @Test
    void testUpdateReturnMap() throws Exception {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(true, "Updated", returnMapDTO, HttpStatus.OK.value());

        // Mock IUserContext and AdapterReturnMapClient
        when(userContext.getUserCode()).thenReturn("testUserCode");
        when(adapterReturnMapClient.updateReturnMap(any(UpdateReturnMapRequest.class))).thenReturn(dataResult);

        ObjectMapper objectMapper = new ObjectMapper();
        String jsonRequest = objectMapper.writeValueAsString(request);

        mockMvc.perform(put("/returnMaps/updateReturnMap")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(jsonRequest))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()))
                .andExpect(jsonPath("$.success").value(true))
                .andExpect(jsonPath("$.message").value("Updated"));

        verify(adapterReturnMapClient, times(1)).updateReturnMap(any(UpdateReturnMapRequest.class));
    }
}

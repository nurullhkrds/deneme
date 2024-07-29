import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import com.example.service.IReturnMapService;
import com.example.controller.ReturnMapController;
import com.example.dto.*;
import com.example.context.RequestContext;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import java.util.Arrays;

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
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(Arrays.asList(new ReturnMapDTO()), HttpStatus.OK.value());
        
        when(returnMapService.getAllByReturnMapCode(anyString())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getAllByReturnMapCode")
                .param("returnMapCode", "code"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).getAllByReturnMapCode(anyString());
    }

    @Test
    public void testGetAll() throws Exception {
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(Arrays.asList(new ReturnMapDTO()), HttpStatus.OK.value());

        when(returnMapService.getAll()).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getAll"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).getAll();
    }

    @Test
    public void testGetReturnMapById() throws Exception {
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(new ReturnMapDTO(), HttpStatus.OK.value());

        when(returnMapService.getReturnMapById(anyLong())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/getReturnMapById")
                .param("returnMapCodeId", "1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).getReturnMapById(anyLong());
    }

    @Test
    public void testCreateReturnMap() throws Exception {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(new ReturnMapDTO(), HttpStatus.CREATED.value());

        when(returnMapService.createReturnMap(any(CreateReturnMapRequest.class))).thenReturn(dataResult);

        mockMvc.perform(post("/returnMaps/createReturnMap")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{ /* JSON representation of CreateReturnMapRequest */ }"))
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.CREATED.value()));

        verify(returnMapService, times(1)).createReturnMap(any(CreateReturnMapRequest.class));
    }

    @Test
    public void testUpdateReturnMap() throws Exception {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        DataResult<ReturnMapDTO> dataResult = new DataResult<>(new ReturnMapDTO(), HttpStatus.OK.value());

        when(returnMapService.updateReturnMap(any(UpdateReturnMapRequest.class))).thenReturn(dataResult);

        mockMvc.perform(put("/returnMaps/updateReturnMap")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{ /* JSON representation of UpdateReturnMapRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).updateReturnMap(any(UpdateReturnMapRequest.class));
    }

    @Test
    public void testSearchReturnMaps() throws Exception {
        DataResult<List<ReturnMapDTO>> dataResult = new DataResult<>(Arrays.asList(new ReturnMapDTO()), HttpStatus.OK.value());

        when(returnMapService.searchReturnMaps(anyString(), anyString(), anyString())).thenReturn(dataResult);

        mockMvc.perform(get("/returnMaps/search")
                .param("returnMapCode", "code")
                .param("bankReturnCode", "bankCode")
                .param("institutionReturnCode", "instCode"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).searchReturnMaps(anyString(), anyString(), anyString());
    }

    @Test
    public void testDeleteReturnMaps() throws Exception {
        DeleteIdsRequest request = new DeleteIdsRequest();
        Result result = new Result(HttpStatus.OK.value());

        when(returnMapService.deleteReturnMaps(any(DeleteIdsRequest.class))).thenReturn(result);

        mockMvc.perform(delete("/returnMaps/delete")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{ /* JSON representation of DeleteIdsRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).deleteReturnMaps(any(DeleteIdsRequest.class));
    }

    @Test
    public void testCopyReturnMaps() throws Exception {
        CopyForIdsAndDataRequest request = new CopyForIdsAndDataRequest();
        Result result = new Result(HttpStatus.OK.value());

        when(returnMapService.copyReturnMaps(any(CopyForIdsAndDataRequest.class))).thenReturn(result);

        mockMvc.perform(post("/returnMaps/copy")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{ /* JSON representation of CopyForIdsAndDataRequest */ }"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.statusCode").value(HttpStatus.OK.value()));

        verify(returnMapService, times(1)).copyReturnMaps(any(CopyForIdsAndDataRequest.class));
    }
}

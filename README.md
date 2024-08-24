import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import com.example.yourpackage.dto.ReturnMapDTO;
import com.example.yourpackage.entity.ReturnMap;
import com.example.yourpackage.entity.ReturnMapDefinition;
import com.example.yourpackage.repository.ReturnMapRepository;
import com.example.yourpackage.service.ReturnMapService;
import com.example.yourpackage.mapper.ReturnMapMapper;
import com.example.yourpackage.request.CreateReturnMapRequest;
import com.example.yourpackage.request.UpdateReturnMapRequest;
import com.example.yourpackage.request.DeleteIdsRequest;
import com.example.yourpackage.result.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.*;

class ReturnMapServiceTest {

    @Mock
    private ReturnMapRepository returnMapRepository;

    @Mock
    private ReturnMapMapper returnMapMapper;

    @Mock
    private IReturnMapDefinitionService returnMapDefinitionService;

    @InjectMocks
    private ReturnMapService returnMapService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetAll_Success() {
        List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
        List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

        when(returnMapRepository.findAll()).thenReturn(mockReturnMapList);
        when(returnMapMapper.toReturnMapDTOList(mockReturnMapList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAll();

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDtoList, result.getData());
    }

    @Test
    void testGetAll_NoDataFound() {
        when(returnMapRepository.findAll()).thenReturn(Collections.emptyList());

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAll();

        assertFalse(result.isSuccess());
        assertEquals(404, result.getStatusCode());
        assertTrue(result.getData().isEmpty());
    }

    @Test
    void testGetAllByReturnMapCode_Success() {
        String returnMapCode = "testCode";
        List<ReturnMap> mockReturnMapList = Arrays.asList(new ReturnMap());
        List<ReturnMapDTO> mockDtoList = Arrays.asList(new ReturnMapDTO());

        when(returnMapRepository.findByReturnMapCode(returnMapCode)).thenReturn(mockReturnMapList);
        when(returnMapMapper.toReturnMapDTOList(mockReturnMapList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllByReturnMapCode(returnMapCode);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDtoList, result.getData());
    }

    @Test
    void testGetAllByReturnMapCode_NoDataFound() {
        String returnMapCode = "testCode";

        when(returnMapRepository.findByReturnMapCode(returnMapCode)).thenReturn(Collections.emptyList());

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllByReturnMapCode(returnMapCode);

        assertFalse(result.isSuccess());
        assertEquals(404, result.getStatusCode());
        assertTrue(result.getData().isEmpty());
    }

    @Test
    void testCreateReturnMap_Success() {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("code1");
        request.setInstitutionReturnText("text1");
        request.setBankReturnCode("code2");
        request.setBankReturnText("text2");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");

        ReturnMapDefinition mockDefinition = new ReturnMapDefinition();
        mockDefinition.setReturnMapCode("testCode");

        when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId()))
                .thenReturn(new SuccessDataResult<>(mockDefinition, 200));

        ReturnMap mockReturnMap = new ReturnMap();
        when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(mockReturnMap);

        ReturnMapDTO mockDto = new ReturnMapDTO();
        when(returnMapMapper.toReturnMapDTO(mockReturnMap)).thenReturn(mockDto);

        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testCreateReturnMap_ReturnMapDefinitionNotFound() {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);

        when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId()))
                .thenReturn(new ErrorDataResult<>(null, 400));

        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
        assertNull(result.getData());
    }

    @Test
    void testUpdateReturnMap_Success() {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("code1");
        request.setInstitutionReturnText("text1");
        request.setBankReturnCode("code2");
        request.setBankReturnText("text2");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");

        ReturnMap mockReturnMap = new ReturnMap();
        ReturnMapDefinition mockDefinition = new ReturnMapDefinition();
        mockDefinition.setReturnMapCode("testCode");

        when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId()))
                .thenReturn(new SuccessDataResult<>(mockDefinition, 200));
        when(returnMapRepository.findById(request.getId()))
                .thenReturn(Optional.of(mockReturnMap));

        when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(mockReturnMap);

        ReturnMapDTO mockDto = new ReturnMapDTO();
        when(returnMapMapper.toReturnMapDTO(mockReturnMap)).thenReturn(mockDto);

        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testUpdateReturnMap_ReturnMapNotFound() {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);

        when(returnMapRepository.findById(request.getId()))
                .thenReturn(Optional.empty());

        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
        assertNull(result.getData());
    }

    @Test
    void testDeleteReturnMaps_Success() {
        DeleteIdsRequest request = new DeleteIdsRequest();
        request.setIds(Arrays.asList(1L, 2L));

        List<ReturnMap> mockReturnMaps = Arrays.asList(new ReturnMap(), new ReturnMap());

        when(returnMapRepository.findAllById(request.getIds())).thenReturn(mockReturnMaps);

        Result result = returnMapService.deleteReturnMaps(request);

        verify(returnMapRepository, times(1)).deleteAll(mockReturnMaps);
        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
    }

    @Test
    void testCopyReturnMaps_Success() {
        CopyForIdsAndDataRequest request = new CopyForIdsAndDataRequest();
        request.setIds(Arrays.asList(1L, 2L));
        request.setReturnMapCode("newCode");

        List<ReturnMap> mockReturnMaps = Arrays.asList(new ReturnMap(), new ReturnMap());

        when(returnMapRepository.findAllById(request.getIds())).thenReturn(mockReturnMaps);

        Result result = returnMapService.copyReturnMaps(request);

        verify(returnMapRepository, times(1)).saveAll(anyList());
        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
    }
}

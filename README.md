import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import com.example.yourpackage.dto.ReturnMapDefinitionDTO;
import com.example.yourpackage.entity.ReturnMapDefinition;
import com.example.yourpackage.repository.ReturnMapDefinitionRepository;
import com.example.yourpackage.service.ReturnMapDefinitionService;
import com.example.yourpackage.mapper.ReturnMapDefinitionMapper;
import com.example.yourpackage.request.CreateReturnMapDefinitionRequest;
import com.example.yourpackage.request.DeleteIdsRequest;
import com.example.yourpackage.request.UpdateReturnMapDefinitionRequest;
import com.example.yourpackage.result.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.*;

class ReturnMapDefinitionServiceTest {

    @Mock
    private ReturnMapDefinitionRepository returnMapDefinitionRepository;

    @Mock
    private ReturnMapDefinitionMapper returnMapDefinitionMapper;

    @InjectMocks
    private ReturnMapDefinitionService returnMapDefinitionService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetAllReturnMapDefinitionReturnMap() {
        List<ReturnMapDefinition> mockDefinitionList = Arrays.asList(new ReturnMapDefinition());
        List<ReturnMapDefinitionDTO> mockDtoList = Arrays.asList(new ReturnMapDefinitionDTO());

        when(returnMapDefinitionRepository.findAll()).thenReturn(mockDefinitionList);
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTOList(mockDefinitionList)).thenReturn(mockDtoList);

        DataResult<List<ReturnMapDefinitionDTO>> result = returnMapDefinitionService.getAllReturnMapDefinitionReturnMap();

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDtoList, result.getData());
    }

    @Test
    void testGetReturnMapDefinitionByReturnMapCode_RecordExists() {
        String returnMapCode = "testCode";
        ReturnMapDefinition mockDefinition = new ReturnMapDefinition();
        ReturnMapDefinitionDTO mockDto = new ReturnMapDefinitionDTO();
        List<String> institutions = Arrays.asList("Institution1", "Institution2");

        when(returnMapDefinitionRepository.findByReturnMapCode(returnMapCode)).thenReturn(Optional.of(mockDefinition));
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTO(mockDefinition)).thenReturn(mockDto);
        when(returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode)).thenReturn(institutions);

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
        assertEquals(institutions, result.getData().getInstitutions());
    }

    @Test
    void testGetReturnMapDefinitionByReturnMapCode_RecordNotExists() {
        String returnMapCode = "testCode";

        when(returnMapDefinitionRepository.findByReturnMapCode(returnMapCode)).thenReturn(Optional.empty());

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);

        assertFalse(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertNull(result.getData());
    }

    @Test
    void testCreateReturnMapDefinition_RecordAlreadyExists() {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        request.setReturnMapCode("existingCode");

        when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.of(new ReturnMapDefinition()));

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.createReturnMapDefinition(request);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
    }

    @Test
    void testCreateReturnMapDefinition_SuccessfulCreation() {
        CreateReturnMapDefinitionRequest request = new CreateReturnMapDefinitionRequest();
        request.setReturnMapCode("newCode");
        request.setIsActive(true);

        ReturnMapDefinition mockEntity = new ReturnMapDefinition();
        ReturnMapDefinition savedEntity = new ReturnMapDefinition();
        ReturnMapDefinitionDTO mockDto = new ReturnMapDefinitionDTO();

        when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.empty());
        when(returnMapDefinitionRepository.save(any(ReturnMapDefinition.class))).thenReturn(savedEntity);
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedEntity)).thenReturn(mockDto);

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.createReturnMapDefinition(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testUpdateReturnMapDefinition_SuccessfulUpdate() {
        UpdateReturnMapDefinitionRequest request = new UpdateReturnMapDefinitionRequest();
        request.setId(1L);
        request.setReturnMapCode("updatedCode");
        request.setIsActive(true);

        ReturnMapDefinition existingReturnMap = new ReturnMapDefinition();
        ReturnMapDefinition updatedEntity = new ReturnMapDefinition();
        ReturnMapDefinitionDTO mockDto = new ReturnMapDefinitionDTO();

        when(returnMapDefinitionRepository.findById(request.getId())).thenReturn(Optional.of(existingReturnMap));
        when(returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode())).thenReturn(Optional.empty());
        when(returnMapDefinitionRepository.save(existingReturnMap)).thenReturn(updatedEntity);
        when(returnMapDefinitionMapper.toReturnMapDefinitionDTO(updatedEntity)).thenReturn(mockDto);

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.updateReturnMapDefinition(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertEquals(mockDto, result.getData());
    }

    @Test
    void testDeleteReturnMapDefinitions_SuccessfulDeletion() {
        DeleteIdsRequest request = new DeleteIdsRequest();
        request.setIds(Arrays.asList(1L, 2L));

        List<ReturnMapDefinition> mockReturnMaps = Arrays.asList(new ReturnMapDefinition(), new ReturnMapDefinition());

        when(returnMapDefinitionRepository.findAllById(request.getIds())).thenReturn(mockReturnMaps);

        Result result = returnMapDefinitionService.deleteReturnMapDefinitions(request);

        verify(returnMapDefinitionRepository, times(1)).deleteAll(mockReturnMaps);
        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
    }
}

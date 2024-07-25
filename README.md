import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import com.ykb.payments.bill.transaction.adapter.core.utilities.*;
import com.ykb.payments.bill.transaction.adapter.domain.ReturnMap;
import com.ykb.payments.bill.transaction.adapter.dto.ReturnMapDTO;
import com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType;
import com.ykb.payments.bill.transaction.adapter.mapper.ReturnMapMapper;
import com.ykb.payments.bill.transaction.adapter.repository.ReturnMapRepository;
import com.ykb.payments.bill.transaction.adapter.web.request.CreateReturnMapRequest;
import com.ykb.payments.bill.transaction.adapter.web.request.UpdateReturnMapRequest;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;

import java.util.Collections;
import java.util.List;
import java.util.Optional;

class ReturnMapServiceTest {

    @Mock
    private ReturnMapRepository returnMapRepository;

    @Mock
    private ReturnMapMapper returnMapMapper;

    @InjectMocks
    private ReturnMapService returnMapService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetAll() {
        ReturnMap returnMap = new ReturnMap();
        when(returnMapRepository.findAll()).thenReturn(Collections.singletonList(returnMap));
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        when(returnMapMapper.toReturnMapDTOList(any())).thenReturn(Collections.singletonList(returnMapDTO));

        DataResult<List<ReturnMapDTO>> result = returnMapService.getAll();

        assertTrue(result.isSuccess());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
        assertFalse(result.getData().isEmpty());
    }

    @Test
    void testCreateReturnMap() {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapCode("code");
        request.setInstitutionReturnCode("instCode");
        request.setInstitutionReturnText("instText");
        request.setBankReturnCode("bankCode");
        request.setBankReturnText("bankText");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");

        ReturnMap returnMap = new ReturnMap();
        when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
        assertNotNull(result.getData());
    }

    @Test
    void testUpdateReturnMap() {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapCode("code");
        request.setInstitutionReturnCode("instCode");
        request.setInstitutionReturnText("instText");
        request.setBankReturnCode("bankCode");
        request.setBankReturnText("bankText");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");

        ReturnMap returnMap = new ReturnMap();
        when(returnMapRepository.findById(request.getId())).thenReturn(Optional.of(returnMap));
        when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);
        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        when(returnMapMapper.toReturnMapDTO(any(ReturnMap.class))).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
        assertNotNull(result.getData());
    }

    @Test
    void testDeleteReturnMaps() {
        List<Long> ids = Collections.singletonList(1L);
        ReturnMap returnMap = new ReturnMap();
        when(returnMapRepository.findAllById(ids)).thenReturn(Collections.singletonList(returnMap));

        Result result = returnMapService.deleteReturnMaps(ids);

        assertTrue(result.isSuccess());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
    }

    @Test
    void testCopyReturnMaps() {
        CopyForIdsAndDataRequest request = new CopyForIdsAndDataRequest();
        request.setIds(Collections.singletonList(1L));
        request.setReturnMapCode("newCode");

        ReturnMap returnMap = new ReturnMap();
        returnMap.setInstitutionReturnCode("instCode");
        returnMap.setInstitutionReturnText("instText");
        returnMap.setBankReturnCode("bankCode");
        returnMap.setBankReturnText("bankText");
        returnMap.setIsReversible(true);
        returnMap.setReturnType(EnumReturnType.SUCCESS);

        when(returnMapRepository.findAllById(request.getIds())).thenReturn(Collections.singletonList(returnMap));
        when(returnMapRepository.saveAll(anyList())).thenReturn(Collections.singletonList(returnMap));

        Result result = returnMapService.copyReturnMaps(request);

        assertTrue(result.isSuccess());
        assertEquals(HttpStatus.OK.value(), result.getStatusCode());
    }
}

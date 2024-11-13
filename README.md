package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionProcessService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionProcessWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionProcessDTO;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

public class AdminInstitutionProcessControllerTest {

    @InjectMocks
    private AdminInstitutionProcessController adminInstitutionProcessController;

    @Mock
    private AdminInstitutionProcessService institutionProcessService;

    @Mock
    private AdminInstitutionProcessMapper institutionProcessMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionProcess_ShouldReturnListOfInstitutionProcessWebDTO() {
        List<InstitutionProcessDTO> institutionProcessDTOList = List.of(new InstitutionProcessDTO());
        List<InstitutionProcessWebDTO> institutionProcessWebDTOList = List.of(new InstitutionProcessWebDTO());

        when(institutionProcessService.getAllInstitutionProcess()).thenReturn(institutionProcessDTOList);
        when(institutionProcessMapper.toWebDTOList(institutionProcessDTOList)).thenReturn(institutionProcessWebDTOList);

        ResponseEntity<DataResult<List<InstitutionProcessWebDTO>>> response = adminInstitutionProcessController.getAllInstitutionProcess();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionProcessWebDTOList, response.getBody().getData());
        verify(institutionProcessService, times(1)).getAllInstitutionProcess();
        verify(institutionProcessMapper, times(1)).toWebDTOList(institutionProcessDTOList);
    }

    @Test
    void getInstitutionProcessById_ShouldReturnInstitutionProcessWebDTO() {
        Long id = 1L;
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionProcessWebDTO institutionProcessWebDTO = new InstitutionProcessWebDTO();

        when(institutionProcessService.getInstitutionProcessById(id)).thenReturn(institutionProcessDTO);
        when(institutionProcessMapper.toWebDTO(institutionProcessDTO)).thenReturn(institutionProcessWebDTO);

        ResponseEntity<DataResult<InstitutionProcessWebDTO>> response = adminInstitutionProcessController.getInstitutionProcessById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionProcessWebDTO, response.getBody().getData());
        verify(institutionProcessService, times(1)).getInstitutionProcessById(id);
        verify(institutionProcessMapper, times(1)).toWebDTO(institutionProcessDTO);
    }

    @Test
    void createInstitutionProcess_ShouldReturnCreatedInstitutionProcessWebDTO() throws MicroException {
        CreateInstitutionProcessRequest request = new CreateInstitutionProcessRequest();
        CreateInstitutionProcessRequestDTO requestDTO = new CreateInstitutionProcessRequestDTO();
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionProcessWebDTO institutionProcessWebDTO = new InstitutionProcessWebDTO();

        when(institutionProcessMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionProcessService.createInstitutionProcess(requestDTO)).thenReturn(institutionProcessDTO);
        when(institutionProcessMapper.toWebDTO(institutionProcessDTO)).thenReturn(institutionProcessWebDTO);

        ResponseEntity<DataResult<InstitutionProcessWebDTO>> response = adminInstitutionProcessController.createInstitutionProcess(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionProcessWebDTO, response.getBody().getData());
        verify(institutionProcessMapper, times(1)).toRequestDTO(request);
        verify(institutionProcessService, times(1)).createInstitutionProcess(requestDTO);
        verify(institutionProcessMapper, times(1)).toWebDTO(institutionProcessDTO);
    }

    @Test
    void updateInstitutionProcess_ShouldReturnUpdatedInstitutionProcessWebDTO() throws MicroException {
        UpdateInstitutionProcessRequest request = new UpdateInstitutionProcessRequest();
        UpdateInstitutionProcessRequestDTO requestDTO = new UpdateInstitutionProcessRequestDTO();
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionProcessWebDTO institutionProcessWebDTO = new InstitutionProcessWebDTO();

        when(institutionProcessMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionProcessService.updateInstitutionProcess(requestDTO)).thenReturn(institutionProcessDTO);
        when(institutionProcessMapper.toWebDTO(institutionProcessDTO)).thenReturn(institutionProcessWebDTO);

        ResponseEntity<DataResult<InstitutionProcessWebDTO>> response = adminInstitutionProcessController.updateInstitutionProcess(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionProcessWebDTO, response.getBody().getData());
        verify(institutionProcessMapper, times(1)).toRequestDTO(request);
        verify(institutionProcessService, times(1)).updateInstitutionProcess(requestDTO);
        verify(institutionProcessMapper, times(1)).toWebDTO(institutionProcessDTO);
    }
}

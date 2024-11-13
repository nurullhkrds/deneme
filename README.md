package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlProccesService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChannelProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChannelProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlProcessWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelProcessDTO;
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

public class AdminInstitutionChnlProccesControllerTest {

    @InjectMocks
    private AdminInstitutionChnlProccesController adminInstitutionChnlProccesController;

    @Mock
    private AdminInstitutionChnlProccesService institutionChnlProccesService;

    @Mock
    private AdminInstitutionChannelProcessMapper institutionChannelProcessMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChnlProcceses_ShouldReturnListOfInstitutionChnlProcessWebDTO() {
        List<InstitutionChnlProcessWebDTO> institutionChnlProcessWebDTOList = List.of(new InstitutionChnlProcessWebDTO());

        when(institutionChnlProccesService.getAllInstitutionChnlProcceses()).thenReturn(institutionChnlProcessWebDTOList);

        ResponseEntity<DataResult<List<InstitutionChnlProcessWebDTO>>> response = adminInstitutionChnlProccesController.getAllInstitutionChnlProcceses();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlProcessWebDTOList, response.getBody().getData());
        verify(institutionChnlProccesService, times(1)).getAllInstitutionChnlProcceses();
    }

    @Test
    void getInstitutionChannelProccesById_ShouldReturnInstitutionChnlProcessWebDTO() {
        Long id = 1L;
        InstitutionChannelProcessDTO institutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        InstitutionChnlProcessWebDTO institutionChnlProcessWebDTO = new InstitutionChnlProcessWebDTO();

        when(institutionChnlProccesService.getInstitutionChannelProccesById(id)).thenReturn(institutionChannelProcessDTO);
        when(institutionChannelProcessMapper.toWebDTO(institutionChannelProcessDTO)).thenReturn(institutionChnlProcessWebDTO);

        ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> response = adminInstitutionChnlProccesController.getInstitutionChannelProccesById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlProcessWebDTO, response.getBody().getData());
        verify(institutionChnlProccesService, times(1)).getInstitutionChannelProccesById(id);
        verify(institutionChannelProcessMapper, times(1)).toWebDTO(institutionChannelProcessDTO);
    }

    @Test
    void createInstitutionChannelProcces_ShouldReturnCreatedInstitutionChnlProcessWebDTO() throws MicroException {
        CreateInstitutionChannelProcessRequest request = new CreateInstitutionChannelProcessRequest();
        CreateInstitutionChannelProcessRequestDTO requestDTO = new CreateInstitutionChannelProcessRequestDTO();
        InstitutionChannelProcessDTO institutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        InstitutionChnlProcessWebDTO institutionChnlProcessWebDTO = new InstitutionChnlProcessWebDTO();

        when(institutionChannelProcessMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlProccesService.createInstitutionChannelProcces(requestDTO)).thenReturn(institutionChannelProcessDTO);
        when(institutionChannelProcessMapper.toWebDTO(institutionChannelProcessDTO)).thenReturn(institutionChnlProcessWebDTO);

        ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> response = adminInstitutionChnlProccesController.createInstitutionChannelProcces(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlProcessWebDTO, response.getBody().getData());
        verify(institutionChannelProcessMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlProccesService, times(1)).createInstitutionChannelProcces(requestDTO);
        verify(institutionChannelProcessMapper, times(1)).toWebDTO(institutionChannelProcessDTO);
    }

    @Test
    void updateInstitutionChannelProcces_ShouldReturnUpdatedInstitutionChnlProcessWebDTO() throws MicroException {
        UpdateInstitutionChannelProcessRequest request = new UpdateInstitutionChannelProcessRequest();
        UpdateInstitutionChannelProcessRequestDTO requestDTO = new UpdateInstitutionChannelProcessRequestDTO();
        InstitutionChannelProcessDTO institutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        InstitutionChnlProcessWebDTO institutionChnlProcessWebDTO = new InstitutionChnlProcessWebDTO();

        when(institutionChannelProcessMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlProccesService.updateInstitutionChannelProcces(requestDTO)).thenReturn(institutionChannelProcessDTO);
        when(institutionChannelProcessMapper.toWebDTO(institutionChannelProcessDTO)).thenReturn(institutionChnlProcessWebDTO);

        ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> response = adminInstitutionChnlProccesController.updateInstitutionChannelProcces(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlProcessWebDTO, response.getBody().getData());
        verify(institutionChannelProcessMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlProccesService, times(1)).updateInstitutionChannelProcces(requestDTO);
        verify(institutionChannelProcessMapper, times(1)).toWebDTO(institutionChannelProcessDTO);
    }
}

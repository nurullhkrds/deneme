package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnlPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMethodWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
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

public class AdminInstitutionChnlPymMethodControllerTest {

    @InjectMocks
    private AdminInstitutionChnlPymMethodController adminInstitutionChnlPymMethodController;

    @Mock
    private AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;

    @Mock
    private AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannelPymMethods_ShouldReturnListOfInstitutionChnlPymMethodWebDTO() {
        List<InstitutionChannelPymMethodDTO> institutionChannelPymMethodDTOList = List.of(new InstitutionChannelPymMethodDTO());
        List<InstitutionChnlPymMethodWebDTO> institutionChnlPymMethodWebDTOList = List.of(new InstitutionChnlPymMethodWebDTO());

        when(institutionChnlPymMethodService.getAll()).thenReturn(institutionChannelPymMethodDTOList);
        when(institutionChnlPymMethodMapper.toWebDTOList(institutionChannelPymMethodDTOList)).thenReturn(institutionChnlPymMethodWebDTOList);

        ResponseEntity<DataResult<List<InstitutionChnlPymMethodWebDTO>>> response = adminInstitutionChnlPymMethodController.getAllInstitutionChannelPymMethods();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMethodWebDTOList, response.getBody().getData());
        verify(institutionChnlPymMethodService, times(1)).getAll();
        verify(institutionChnlPymMethodMapper, times(1)).toWebDTOList(institutionChannelPymMethodDTOList);
    }

    @Test
    void getInstitutionChannelPymMethodById_ShouldReturnInstitutionChnlPymMethodWebDTO() {
        Long id = 1L;
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnlPymMethodWebDTO institutionChnlPymMethodWebDTO = new InstitutionChnlPymMethodWebDTO();

        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(id)).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnlPymMethodMapper.toWebDTO(institutionChannelPymMethodDTO)).thenReturn(institutionChnlPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> response = adminInstitutionChnlPymMethodController.getInstitutionChannelPymMethodById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMethodWebDTO, response.getBody().getData());
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(id);
        verify(institutionChnlPymMethodMapper, times(1)).toWebDTO(institutionChannelPymMethodDTO);
    }

    @Test
    void createInstitutionChannelPymMethod_ShouldReturnCreatedInstitutionChnlPymMethodWebDTO() throws MicroException {
        CreateInstitutionChnlPymMethodRequest request = new CreateInstitutionChnlPymMethodRequest();
        CreateInstitutionChnlPymMethodRequestDTO requestDTO = new CreateInstitutionChnlPymMethodRequestDTO();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnlPymMethodWebDTO institutionChnlPymMethodWebDTO = new InstitutionChnlPymMethodWebDTO();

        when(institutionChnlPymMethodMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMethodService.createInstitutionChannelPymMethod(requestDTO)).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnlPymMethodMapper.toWebDTO(institutionChannelPymMethodDTO)).thenReturn(institutionChnlPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> response = adminInstitutionChnlPymMethodController.createInstitutionChannelPymMethod(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHANNEL_PYM_METHOD_CREATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMethodWebDTO, response.getBody().getData());
        verify(institutionChnlPymMethodMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMethodService, times(1)).createInstitutionChannelPymMethod(requestDTO);
        verify(institutionChnlPymMethodMapper, times(1)).toWebDTO(institutionChannelPymMethodDTO);
    }

    @Test
    void updateInstitutionChannelPymMethod_ShouldReturnUpdatedInstitutionChnlPymMethodWebDTO() throws MicroException {
        UpdateInstitutionChnlPymMethodRequest request = new UpdateInstitutionChnlPymMethodRequest();
        UpdateInstitutionChnlPymMethodRequestDTO requestDTO = new UpdateInstitutionChnlPymMethodRequestDTO();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnlPymMethodWebDTO institutionChnlPymMethodWebDTO = new InstitutionChnlPymMethodWebDTO();

        when(institutionChnlPymMethodMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMethodService.updateInstitutionChannelPymMethod(requestDTO)).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnlPymMethodMapper.toWebDTO(institutionChannelPymMethodDTO)).thenReturn(institutionChnlPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> response = adminInstitutionChnlPymMethodController.updateInstitutionChannelPymMethod(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHANNEL_PYM_METHOD_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMethodWebDTO, response.getBody().getData());
        verify(institutionChnlPymMethodMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMethodService, times(1)).updateInstitutionChannelPymMethod(requestDTO);
        verify(institutionChnlPymMethodMapper, times(1)).toWebDTO(institutionChannelPymMethodDTO);
    }
}

package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdAccMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAccService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMthdAccRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMthdAccRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdAccWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO;
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

public class AdminInstitutionChnlPymMthdAccControllerTest {

    @InjectMocks
    private AdminInstitutionChnlPymMthdAccController adminInstitutionChnlPymMthdAccController;

    @Mock
    private AdminInstitutionChnlPymMthdAccService institutionChnlPymMthdAccService;

    @Mock
    private AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannelPymMethodsAcc_ShouldReturnListOfInstitutionChnlPymMthdAccWebDTO() {
        List<InstitutionChnlPymMthdAccWebDTO> institutionChnlPymMthdAccWebDTOList = List.of(new InstitutionChnlPymMthdAccWebDTO());

        when(institutionChnlPymMthdAccService.getAllInstitutionChannelPymMethodsAcc()).thenReturn(institutionChnlPymMthdAccWebDTOList);

        ResponseEntity<DataResult<List<InstitutionChnlPymMthdAccWebDTO>>> response = adminInstitutionChnlPymMthdAccController.getAllInstitutionChannelPymMethodsAcc();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdAccWebDTOList, response.getBody().getData());
        verify(institutionChnlPymMthdAccService, times(1)).getAllInstitutionChannelPymMethodsAcc();
    }

    @Test
    void getInstitutionChannelPymMethodAccById_ShouldReturnInstitutionChnlPymMthdAccWebDTO() {
        Long id = 1L;
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        InstitutionChnlPymMthdAccWebDTO institutionChnlPymMthdAccWebDTO = new InstitutionChnlPymMthdAccWebDTO();

        when(institutionChnlPymMthdAccService.getInstitutionChannelPymMethodAccById(id)).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccMapper.toWebDTO(institutionChnnlPymMthdAccDTO)).thenReturn(institutionChnlPymMthdAccWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> response = adminInstitutionChnlPymMthdAccController.getInstitutionChannelPymMethodAccById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdAccWebDTO, response.getBody().getData());
        verify(institutionChnlPymMthdAccService, times(1)).getInstitutionChannelPymMethodAccById(id);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toWebDTO(institutionChnnlPymMthdAccDTO);
    }

    @Test
    void createInstitutionChannelPymMethodAcc_ShouldReturnCreatedInstitutionChnlPymMthdAccWebDTO() throws MicroException {
        CreateInstitutionChnlPymMthdAccRequest request = new CreateInstitutionChnlPymMthdAccRequest();
        CreateInstitutionChnlPymMthdAccRequestDTO requestDTO = new CreateInstitutionChnlPymMthdAccRequestDTO();
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        InstitutionChnlPymMthdAccWebDTO institutionChnlPymMthdAccWebDTO = new InstitutionChnlPymMthdAccWebDTO();

        when(institutionChnnlPymMthdAccMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMthdAccService.createInstitutionChannelPymMethodAcc(requestDTO)).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccMapper.toWebDTO(institutionChnnlPymMthdAccDTO)).thenReturn(institutionChnlPymMthdAccWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> response = adminInstitutionChnlPymMthdAccController.createInstitutionChannelPymMethodAcc(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHNNL_PYM_MTHD_ACC_CREATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdAccWebDTO, response.getBody().getData());
        verify(institutionChnnlPymMthdAccMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMthdAccService, times(1)).createInstitutionChannelPymMethodAcc(requestDTO);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toWebDTO(institutionChnnlPymMthdAccDTO);
    }

    @Test
    void updateInstitutionChannelPymMethodAcc_ShouldReturnUpdatedInstitutionChnlPymMthdAccWebDTO() throws MicroException {
        UpdateInstitutionChnlPymMthdAccRequest request = new UpdateInstitutionChnlPymMthdAccRequest();
        UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdAccRequestDTO();
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        InstitutionChnlPymMthdAccWebDTO institutionChnlPymMthdAccWebDTO = new InstitutionChnlPymMthdAccWebDTO();

        when(institutionChnnlPymMthdAccMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMthdAccService.updateInstitutionChannelPymMethodAcc(requestDTO)).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccMapper.toWebDTO(institutionChnnlPymMthdAccDTO)).thenReturn(institutionChnlPymMthdAccWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> response = adminInstitutionChnlPymMthdAccController.updateInstitutionChannelPymMethodAcc(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHNNL_PYM_MTHD_ACC_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdAccWebDTO, response.getBody().getData());
        verify(institutionChnnlPymMthdAccMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMthdAccService, times(1)).updateInstitutionChannelPymMethodAcc(requestDTO);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toWebDTO(institutionChnnlPymMthdAccDTO);
    }
}

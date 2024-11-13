package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionUserIntfMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionUserIntfService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionUserIntfRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionUserIntfRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionUserIntfWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class AdminInstitutionUserIntfControllerTest {

    @InjectMocks
    private AdminInstitutionUserIntfController adminInstitutionUserIntfController;

    @Mock
    private AdminInstitutionUserIntfService institutionUserIntfService;

    @Mock
    private AdminInstitutionUserIntfMapper institutionUserIntfMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionUserIntfs_ShouldReturnListOfInstitutionUserIntfWebDTO() {
        List<InstitutionUserIntfWebDTO> institutionUserIntfWebDTOList = List.of(new InstitutionUserIntfWebDTO());

        when(institutionUserIntfService.getAllInstitutionUserIntfs()).thenReturn(institutionUserIntfWebDTOList);

        ResponseEntity<DataResult<List<InstitutionUserIntfWebDTO>>> response = adminInstitutionUserIntfController.getAllInstitutionUserIntfs();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionUserIntfWebDTOList, response.getBody().getData());
        verify(institutionUserIntfService, times(1)).getAllInstitutionUserIntfs();
    }

    @Test
    void getInstitutionUserIntfById_ShouldReturnInstitutionUserIntfWebDTO() {
        Long id = 1L;
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        InstitutionUserIntfWebDTO institutionUserIntfWebDTO = new InstitutionUserIntfWebDTO();

        when(institutionUserIntfService.getInstitutionUserIntfById(id)).thenReturn(institutionUserIntfDTO);
        when(institutionUserIntfMapper.toWebDTO(institutionUserIntfDTO)).thenReturn(institutionUserIntfWebDTO);

        ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> response = adminInstitutionUserIntfController.getInstitutionUserIntfById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionUserIntfWebDTO, response.getBody().getData());
        verify(institutionUserIntfService, times(1)).getInstitutionUserIntfById(id);
        verify(institutionUserIntfMapper, times(1)).toWebDTO(institutionUserIntfDTO);
    }

    @Test
    void createInstitutionUserIntf_ShouldReturnCreatedInstitutionUserIntfWebDTO() throws MicroException {
        CreateInstitutionUserIntfRequest request = new CreateInstitutionUserIntfRequest();
        CreateInstitutionUserIntfRequestDTO requestDTO = new CreateInstitutionUserIntfRequestDTO();
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        InstitutionUserIntfWebDTO institutionUserIntfWebDTO = new InstitutionUserIntfWebDTO();

        when(institutionUserIntfMapper.toCreateDTO(request)).thenReturn(requestDTO);
        when(institutionUserIntfService.createInstitutionUserIntf(requestDTO)).thenReturn(institutionUserIntfDTO);
        when(institutionUserIntfMapper.toWebDTO(institutionUserIntfDTO)).thenReturn(institutionUserIntfWebDTO);

        ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> response = adminInstitutionUserIntfController.createInstitutionUserIntf(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionUserIntfWebDTO, response.getBody().getData());
        verify(institutionUserIntfMapper, times(1)).toCreateDTO(request);
        verify(institutionUserIntfService, times(1)).createInstitutionUserIntf(requestDTO);
        verify(institutionUserIntfMapper, times(1)).toWebDTO(institutionUserIntfDTO);
    }

    @Test
    void updateInstitutionUserIntf_ShouldReturnUpdatedInstitutionUserIntfWebDTO() throws MicroException {
        UpdateInstitutionUserIntfRequest request = new UpdateInstitutionUserIntfRequest();
        UpdateInstitutionUserIntfRequestDTO requestDTO = new UpdateInstitutionUserIntfRequestDTO();
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        InstitutionUserIntfWebDTO institutionUserIntfWebDTO = new InstitutionUserIntfWebDTO();

        when(institutionUserIntfMapper.toUpdateDTO(request)).thenReturn(requestDTO);
        when(institutionUserIntfService.updateInstitutionUserIntf(requestDTO)).thenReturn(institutionUserIntfDTO);
        when(institutionUserIntfMapper.toWebDTO(institutionUserIntfDTO)).thenReturn(institutionUserIntfWebDTO);

        ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> response = adminInstitutionUserIntfController.updateInstitutionUserIntf(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionUserIntfWebDTO, response.getBody().getData());
        verify(institutionUserIntfMapper, times(1)).toUpdateDTO(request);
        verify(institutionUserIntfService, times(1)).updateInstitutionUserIntf(requestDTO);
        verify(institutionUserIntfMapper, times(1)).toWebDTO(institutionUserIntfDTO);
    }
}

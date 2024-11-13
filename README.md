package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionPymMethodWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.mapper.InstitutionPymMethodMapper;
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

public class AdminInstitutionPymMethodControllerTest {

    @InjectMocks
    private AdminInstitutionPymMethodController adminInstitutionPymMethodController;

    @Mock
    private AdminInstitutionPymMethodService adminInstitutionPymMethodService;

    @Mock
    private InstitutionPymMethodMapper institutionPymMethodMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionPymMethods_ShouldReturnListOfInstitutionPymMethodWebDTO() {
        List<InstitutionPymMethodDTO> institutionPymMethodDTOList = List.of(new InstitutionPymMethodDTO());
        List<InstitutionPymMethodWebDTO> institutionPymMethodWebDTOList = List.of(new InstitutionPymMethodWebDTO());

        when(adminInstitutionPymMethodService.getAllInstitutionPymMethods()).thenReturn(institutionPymMethodDTOList);
        when(institutionPymMethodMapper.toWebDTOList(institutionPymMethodDTOList)).thenReturn(institutionPymMethodWebDTOList);

        ResponseEntity<DataResult<List<InstitutionPymMethodWebDTO>>> response = adminInstitutionPymMethodController.getAllInstitutionPymMethods();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionPymMethodWebDTOList, response.getBody().getData());
        verify(adminInstitutionPymMethodService, times(1)).getAllInstitutionPymMethods();
        verify(institutionPymMethodMapper, times(1)).toWebDTOList(institutionPymMethodDTOList);
    }

    @Test
    void getInstitutionPymMethodById_ShouldReturnInstitutionPymMethodWebDTO() {
        Long id = 1L;
        InstitutionPymMethodDTO institutionPymMethodDTO = new InstitutionPymMethodDTO();
        InstitutionPymMethodWebDTO institutionPymMethodWebDTO = new InstitutionPymMethodWebDTO();

        when(adminInstitutionPymMethodService.getInstitutionPymMethodById(id)).thenReturn(institutionPymMethodDTO);
        when(institutionPymMethodMapper.toWebDTO(institutionPymMethodDTO)).thenReturn(institutionPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> response = adminInstitutionPymMethodController.getInstitutionPymMethodById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionPymMethodWebDTO, response.getBody().getData());
        verify(adminInstitutionPymMethodService, times(1)).getInstitutionPymMethodById(id);
        verify(institutionPymMethodMapper, times(1)).toWebDTO(institutionPymMethodDTO);
    }

    @Test
    void createInstitutionPymMethod_ShouldReturnCreatedInstitutionPymMethodWebDTO() throws MicroException {
        CreateInstitutionPymMethodRequest request = new CreateInstitutionPymMethodRequest();
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        InstitutionPymMethodDTO institutionPymMethodDTO = new InstitutionPymMethodDTO();
        InstitutionPymMethodWebDTO institutionPymMethodWebDTO = new InstitutionPymMethodWebDTO();

        when(institutionPymMethodMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO)).thenReturn(institutionPymMethodDTO);
        when(institutionPymMethodMapper.toWebDTO(institutionPymMethodDTO)).thenReturn(institutionPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> response = adminInstitutionPymMethodController.createInstitutionPymMethod(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_PYM_METHOD_CREATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionPymMethodWebDTO, response.getBody().getData());
        verify(institutionPymMethodMapper, times(1)).toRequestDTO(request);
        verify(adminInstitutionPymMethodService, times(1)).createInstitutionPymMethod(requestDTO);
        verify(institutionPymMethodMapper, times(1)).toWebDTO(institutionPymMethodDTO);
    }

    @Test
    void updateInstitutionPymMethod_ShouldReturnUpdatedInstitutionPymMethodWebDTO() throws MicroException {
        UpdateInstitutionPymMethodRequest request = new UpdateInstitutionPymMethodRequest();
        UpdateInstitutionPymMethodRequestDTO requestDTO = new UpdateInstitutionPymMethodRequestDTO();
        InstitutionPymMethodDTO institutionPymMethodDTO = new InstitutionPymMethodDTO();
        InstitutionPymMethodWebDTO institutionPymMethodWebDTO = new InstitutionPymMethodWebDTO();

        when(institutionPymMethodMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(adminInstitutionPymMethodService.updateInstitutionPymMethod(requestDTO)).thenReturn(institutionPymMethodDTO);
        when(institutionPymMethodMapper.toWebDTO(institutionPymMethodDTO)).thenReturn(institutionPymMethodWebDTO);

        ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> response = adminInstitutionPymMethodController.updateInstitutionPymMethod(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_PYM_METHOD_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionPymMethodWebDTO, response.getBody().getData());
        verify(institutionPymMethodMapper, times(1)).toRequestDTO(request);
        verify(adminInstitutionPymMethodService, times(1)).updateInstitutionPymMethod(requestDTO);
        verify(institutionPymMethodMapper, times(1)).toWebDTO(institutionPymMethodDTO);
    }
}

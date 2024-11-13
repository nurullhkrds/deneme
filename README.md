package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdPscMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAPscService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMthdPscRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMthdPscRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdPscWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdPscDTO;
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

public class AdminInstitutionChnlPymMthdPscControllerTest {

    @InjectMocks
    private AdminInstitutionChnlPymMthdPscController adminInstitutionChnlPymMthdPscController;

    @Mock
    private AdminInstitutionChnlPymMthdAPscService institutionChnlPymMthdPscService;

    @Mock
    private AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannelPymMethodsPsc_ShouldReturnListOfInstitutionChnlPymMthdPscWebDTO() {
        List<InstitutionChnlPymMthdPscWebDTO> institutionChnlPymMthdPscWebDTOList = List.of(new InstitutionChnlPymMthdPscWebDTO());

        when(institutionChnlPymMthdPscService.getAllInstitutionChnlPymMthdAPsc()).thenReturn(institutionChnlPymMthdPscWebDTOList);

        ResponseEntity<DataResult<List<InstitutionChnlPymMthdPscWebDTO>>> response = adminInstitutionChnlPymMthdPscController.getAllInstitutionChannelPymMethodsPsc();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdPscWebDTOList, response.getBody().getData());
        verify(institutionChnlPymMthdPscService, times(1)).getAllInstitutionChnlPymMthdAPsc();
    }

    @Test
    void getInstitutionChannelPymMethodPscById_ShouldReturnInstitutionChnlPymMthdPscWebDTO() {
        Long id = 1L;
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        InstitutionChnlPymMthdPscWebDTO institutionChnlPymMthdPscWebDTO = new InstitutionChnlPymMthdPscWebDTO();

        when(institutionChnlPymMthdPscService.getInstitutionChnlPymMthdAPscById(id)).thenReturn(institutionChnnlPymMthdPscDTO);
        when(institutionChnnlPymMthdPscMapper.toWebDTO(institutionChnnlPymMthdPscDTO)).thenReturn(institutionChnlPymMthdPscWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> response = adminInstitutionChnlPymMthdPscController.getInstitutionChannelPymMethodPscById(id);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdPscWebDTO, response.getBody().getData());
        verify(institutionChnlPymMthdPscService, times(1)).getInstitutionChnlPymMthdAPscById(id);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toWebDTO(institutionChnnlPymMthdPscDTO);
    }

    @Test
    void createInstitutionChannelPymMethodPsc_ShouldReturnCreatedInstitutionChnlPymMthdPscWebDTO() throws MicroException {
        CreateInstitutionChnlPymMthdPscRequest request = new CreateInstitutionChnlPymMthdPscRequest();
        CreateInstitutionChnlPymMthdPscRequestDTO requestDTO = new CreateInstitutionChnlPymMthdPscRequestDTO();
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        InstitutionChnlPymMthdPscWebDTO institutionChnlPymMthdPscWebDTO = new InstitutionChnlPymMthdPscWebDTO();

        when(institutionChnnlPymMthdPscMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMthdPscService.createInstitutionChnlPymMthdPsc(requestDTO)).thenReturn(institutionChnnlPymMthdPscDTO);
        when(institutionChnnlPymMthdPscMapper.toWebDTO(institutionChnnlPymMthdPscDTO)).thenReturn(institutionChnlPymMthdPscWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> response = adminInstitutionChnlPymMthdPscController.createInstitutionChannelPymMethodPsc(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdPscWebDTO, response.getBody().getData());
        verify(institutionChnnlPymMthdPscMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMthdPscService, times(1)).createInstitutionChnlPymMthdPsc(requestDTO);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toWebDTO(institutionChnnlPymMthdPscDTO);
    }

    @Test
    void updateInstitutionChannelPymMethodPsc_ShouldReturnUpdatedInstitutionChnlPymMthdPscWebDTO() throws MicroException {
        UpdateInstitutionChnlPymMthdPscRequest request = new UpdateInstitutionChnlPymMthdPscRequest();
        UpdateInstitutionChnlPymMthdPscRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdPscRequestDTO();
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        InstitutionChnlPymMthdPscWebDTO institutionChnlPymMthdPscWebDTO = new InstitutionChnlPymMthdPscWebDTO();

        when(institutionChnnlPymMthdPscMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChnlPymMthdPscService.updateInstitutionChnlPymMthdPsc(requestDTO)).thenReturn(institutionChnnlPymMthdPscDTO);
        when(institutionChnnlPymMthdPscMapper.toWebDTO(institutionChnnlPymMthdPscDTO)).thenReturn(institutionChnlPymMthdPscWebDTO);

        ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> response = adminInstitutionChnlPymMthdPscController.updateInstitutionChannelPymMethodPsc(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChnlPymMthdPscWebDTO, response.getBody().getData());
        verify(institutionChnnlPymMthdPscMapper, times(1)).toRequestDTO(request);
        verify(institutionChnlPymMthdPscService, times(1)).updateInstitutionChnlPymMthdPsc(requestDTO);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toWebDTO(institutionChnnlPymMthdPscDTO);
    }
}

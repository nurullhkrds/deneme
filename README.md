package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionCityMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionCityService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionCityRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionCityRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionCityWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionCityDTO;
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

public class AdminInstitutionCityControllerTest {

    @InjectMocks
    private AdminInsitutionCityController adminInstitutionCityController;

    @Mock
    private AdminInstitutionCityService institutionCityService;

    @Mock
    private AdminInstitutionCityMapper institutionCityMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionCities_ShouldReturnListOfInstitutionCityWebDTO() {
        List<InstitutionCityDTO> institutionCityDTOList = List.of(new InstitutionCityDTO());
        List<InstitutionCityWebDTO> institutionCityWebDTOList = List.of(new InstitutionCityWebDTO());

        when(institutionCityService.getAllInstitutionCities()).thenReturn(institutionCityDTOList);
        when(institutionCityMapper.toWebDTOList(institutionCityDTOList)).thenReturn(institutionCityWebDTOList);

        ResponseEntity<DataResult<List<InstitutionCityWebDTO>>> response = adminInstitutionCityController.getAllInstitutionCities();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionCityWebDTOList, response.getBody().getData());
        verify(institutionCityService, times(1)).getAllInstitutionCities();
        verify(institutionCityMapper, times(1)).toWebDTOList(institutionCityDTOList);
    }

    @Test
    void getInstitutionCityById_ShouldReturnInstitutionCityWebDTO() {
        Long institutionCityId = 1L;
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        InstitutionCityWebDTO institutionCityWebDTO = new InstitutionCityWebDTO();

        when(institutionCityService.getInstitutionCityById(institutionCityId)).thenReturn(institutionCityDTO);
        when(institutionCityMapper.toWebDTO(institutionCityDTO)).thenReturn(institutionCityWebDTO);

        ResponseEntity<DataResult<InstitutionCityWebDTO>> response = adminInstitutionCityController.getInstitutionCityById(institutionCityId);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionCityWebDTO, response.getBody().getData());
        verify(institutionCityService, times(1)).getInstitutionCityById(institutionCityId);
        verify(institutionCityMapper, times(1)).toWebDTO(institutionCityDTO);
    }

    @Test
    void createInstitutionCity_ShouldReturnCreatedInstitutionCityWebDTO() throws MicroException {
        CreateInstitutionCityRequest request = new CreateInstitutionCityRequest();
        CreateInstitutionCityRequestDTO requestDTO = new CreateInstitutionCityRequestDTO();
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        InstitutionCityWebDTO institutionCityWebDTO = new InstitutionCityWebDTO();

        when(institutionCityMapper.toCreateDTO(request)).thenReturn(requestDTO);
        when(institutionCityService.createInstitutionCity(requestDTO)).thenReturn(institutionCityDTO);
        when(institutionCityMapper.toWebDTO(institutionCityDTO)).thenReturn(institutionCityWebDTO);

        ResponseEntity<DataResult<InstitutionCityWebDTO>> response = adminInstitutionCityController.createInstitutionCity(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CITY_CREATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionCityWebDTO, response.getBody().getData());
        verify(institutionCityMapper, times(1)).toCreateDTO(request);
        verify(institutionCityService, times(1)).createInstitutionCity(requestDTO);
        verify(institutionCityMapper, times(1)).toWebDTO(institutionCityDTO);
    }

    @Test
    void updateInstitutionCity_ShouldReturnUpdatedInstitutionCityWebDTO() throws MicroException {
        UpdateInstitutionCityRequest request = new UpdateInstitutionCityRequest();
        UpdateInstitutionCityRequestDTO requestDTO = new UpdateInstitutionCityRequestDTO();
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        InstitutionCityWebDTO institutionCityWebDTO = new InstitutionCityWebDTO();

        when(institutionCityMapper.toUpdateDTO(request)).thenReturn(requestDTO);
        when(institutionCityService.updateInstitutionCity(requestDTO)).thenReturn(institutionCityDTO);
        when(institutionCityMapper.toWebDTO(institutionCityDTO)).thenReturn(institutionCityWebDTO);

        ResponseEntity<DataResult<InstitutionCityWebDTO>> response = adminInstitutionCityController.updateInstitutionCity(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CITY_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionCityWebDTO, response.getBody().getData());
        verify(institutionCityMapper, times(1)).toUpdateDTO(request);
        verify(institutionCityService, times(1)).updateInstitutionCity(requestDTO);
        verify(institutionCityMapper, times(1)).toWebDTO(institutionCityDTO);
    }
}

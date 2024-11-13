package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChannelRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChannelRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChannelWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
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

public class AdminInstitutionChannelControllerTest {

    @InjectMocks
    private AdminInstitutionChannelController adminInstitutionChannelController;

    @Mock
    private AdminInstitutionChannelService institutionChannelService;

    @Mock
    private AdminInstitutionChannelMapper institutionChannelMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannels_ShouldReturnListOfInstitutionChannelWebDTO() {
        List<InstitutionChannelDTO> institutionChannelDTOList = List.of(new InstitutionChannelDTO());
        List<InstitutionChannelWebDTO> institutionChannelWebDTOList = List.of(new InstitutionChannelWebDTO());

        when(institutionChannelService.getAllInstitutionChannels()).thenReturn(institutionChannelDTOList);
        when(institutionChannelMapper.toWebDTOList(institutionChannelDTOList)).thenReturn(institutionChannelWebDTOList);

        ResponseEntity<DataResult<List<InstitutionChannelWebDTO>>> response = adminInstitutionChannelController.getAllInstitutionChannels();

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_LISTED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChannelWebDTOList, response.getBody().getData());
        verify(institutionChannelService, times(1)).getAllInstitutionChannels();
        verify(institutionChannelMapper, times(1)).toWebDTOList(institutionChannelDTOList);
    }

    @Test
    void getInstitutionChannelById_ShouldReturnInstitutionChannelWebDTO() {
        Long institutionChannelId = 1L;
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannelWebDTO institutionChannelWebDTO = new InstitutionChannelWebDTO();

        when(institutionChannelService.getInstitutionChannelById(institutionChannelId)).thenReturn(institutionChannelDTO);
        when(institutionChannelMapper.toWebDTO(institutionChannelDTO)).thenReturn(institutionChannelWebDTO);

        ResponseEntity<DataResult<InstitutionChannelWebDTO>> response = adminInstitutionChannelController.getInstitutionChannelById(institutionChannelId);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.DATA_RETRIEVED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChannelWebDTO, response.getBody().getData());
        verify(institutionChannelService, times(1)).getInstitutionChannelById(institutionChannelId);
        verify(institutionChannelMapper, times(1)).toWebDTO(institutionChannelDTO);
    }

    @Test
    void createInstitutionChannel_ShouldReturnCreatedInstitutionChannelWebDTO() throws MicroException {
        CreateInstitutionChannelRequest request = new CreateInstitutionChannelRequest();
        CreateInstitutionChannelRequestDTO requestDTO = new CreateInstitutionChannelRequestDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannelWebDTO institutionChannelWebDTO = new InstitutionChannelWebDTO();

        when(institutionChannelMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChannelService.createInstitutionChannel(requestDTO)).thenReturn(institutionChannelDTO);
        when(institutionChannelMapper.toWebDTO(institutionChannelDTO)).thenReturn(institutionChannelWebDTO);

        ResponseEntity<DataResult<InstitutionChannelWebDTO>> response = adminInstitutionChannelController.createInstitutionChannel(request);

        assertNotNull(response);
        assertEquals(HttpStatus.CREATED, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHANNEL_CREATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChannelWebDTO, response.getBody().getData());
        verify(institutionChannelMapper, times(1)).toRequestDTO(request);
        verify(institutionChannelService, times(1)).createInstitutionChannel(requestDTO);
        verify(institutionChannelMapper, times(1)).toWebDTO(institutionChannelDTO);
    }

    @Test
    void updateInstitutionChannel_ShouldReturnUpdatedInstitutionChannelWebDTO() throws MicroException {
        UpdateInstitutionChannelRequest request = new UpdateInstitutionChannelRequest();
        UpdateInstitutionChannelRequestDTO requestDTO = new UpdateInstitutionChannelRequestDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannelWebDTO institutionChannelWebDTO = new InstitutionChannelWebDTO();

        when(institutionChannelMapper.toRequestDTO(request)).thenReturn(requestDTO);
        when(institutionChannelService.updateInstitutionChannel(requestDTO)).thenReturn(institutionChannelDTO);
        when(institutionChannelMapper.toWebDTO(institutionChannelDTO)).thenReturn(institutionChannelWebDTO);

        ResponseEntity<DataResult<InstitutionChannelWebDTO>> response = adminInstitutionChannelController.updateInstitutionChannel(request);

        assertNotNull(response);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(ResultConstant.INSTITUTION_CHANNEL_UPDATED.getMessage(), response.getBody().getMessage());
        assertEquals(institutionChannelWebDTO, response.getBody().getData());
        verify(institutionChannelMapper, times(1)).toRequestDTO(request);
        verify(institutionChannelService, times(1)).updateInstitutionChannel(requestDTO);
        verify(institutionChannelMapper, times(1)).toWebDTO(institutionChannelDTO);
    }
}

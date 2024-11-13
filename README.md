package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionUserIntfMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionUserIntfWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionUserIntf;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionUserIntfRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

public class AdminInstitutionUserIntfServiceImplTest {

    @InjectMocks
    private AdminInstitutionUserIntfServiceImpl adminInstitutionUserIntfService;

    @Mock
    private InstitutionUserIntfRepository institutionUserIntfRepository;

    @Mock
    private AdminInstitutionUserIntfMapper institutionUserIntfMapper;

    @Mock
    private AdminInstitutionDebtTypeService institutionDebtTypeService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionUserIntfs_ShouldReturnAllUserInterfaces() {
        List<Object[]> userIntfList = List.of(new Object[0]);
        when(institutionUserIntfRepository.findInstitutionUserIntfWithInstitution()).thenReturn(userIntfList);
        when(institutionUserIntfMapper.objectArrayToWebDTO(any())).thenReturn(new InstitutionUserIntfWebDTO());

        List<InstitutionUserIntfWebDTO> result = adminInstitutionUserIntfService.getAllInstitutionUserIntfs();

        assertNotNull(result);
        assertEquals(1, result.size());
        verify(institutionUserIntfRepository, times(1)).findInstitutionUserIntfWithInstitution();
        verify(institutionUserIntfMapper, times(1)).objectArrayToWebDTO(any());
    }

    @Test
    void getInstitutionUserIntfById_WhenFound_ShouldReturnDTO() {
        InstitutionUserIntf institutionUserIntf = new InstitutionUserIntf();
        when(institutionUserIntfRepository.findById(1L)).thenReturn(Optional.of(institutionUserIntf));
        when(institutionUserIntfMapper.toDTO(institutionUserIntf)).thenReturn(new InstitutionUserIntfDTO());

        InstitutionUserIntfDTO result = adminInstitutionUserIntfService.getInstitutionUserIntfById(1L);

        assertNotNull(result);
        verify(institutionUserIntfRepository, times(1)).findById(1L);
        verify(institutionUserIntfMapper, times(1)).toDTO(institutionUserIntf);
    }

    @Test
    void getInstitutionUserIntfById_WhenNotFound_ShouldReturnNull() {
        when(institutionUserIntfRepository.findById(1L)).thenReturn(Optional.empty());

        InstitutionUserIntfDTO result = adminInstitutionUserIntfService.getInstitutionUserIntfById(1L);

        assertNull(result);
        verify(institutionUserIntfRepository, times(1)).findById(1L);
    }

    @Test
    void createInstitutionUserIntf_WhenDuplicate_ShouldThrowException() {
        CreateInstitutionUserIntfRequestDTO requestDTO = new CreateInstitutionUserIntfRequestDTO();
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setScreenOrderNo(1);

        when(institutionUserIntfRepository.existsByInstitutionDebtTypeIdAndScreenOrderNo(anyLong(), anyInt())).thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionUserIntfService.createInstitutionUserIntf(requestDTO));
        verify(institutionUserIntfRepository, times(1)).existsByInstitutionDebtTypeIdAndScreenOrderNo(anyLong(), anyInt());
    }

    @Test
    void createInstitutionUserIntf_WhenValid_ShouldReturnDTO() throws MicroException {
        CreateInstitutionUserIntfRequestDTO requestDTO = new CreateInstitutionUserIntfRequestDTO();
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setScreenOrderNo(1);
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        InstitutionUserIntf institutionUserIntf = new InstitutionUserIntf();

        when(institutionUserIntfRepository.existsByInstitutionDebtTypeIdAndScreenOrderNo(anyLong(), anyInt())).thenReturn(false);
        when(institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId())).thenReturn(institutionDebtTypeDTO);
        when(institutionUserIntfMapper.toDTO(requestDTO)).thenReturn(institutionUserIntfDTO);
        when(institutionUserIntfMapper.toEntity(institutionUserIntfDTO)).thenReturn(institutionUserIntf);
        when(institutionUserIntfRepository.save(institutionUserIntf)).thenReturn(institutionUserIntf);
        when(institutionUserIntfMapper.toDTO(institutionUserIntf)).thenReturn(institutionUserIntfDTO);

        InstitutionUserIntfDTO result = adminInstitutionUserIntfService.createInstitutionUserIntf(requestDTO);

        assertNotNull(result);
        verify(institutionUserIntfRepository, times(1)).existsByInstitutionDebtTypeIdAndScreenOrderNo(anyLong(), anyInt());
        verify(institutionDebtTypeService, times(1)).getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        verify(institutionUserIntfRepository, times(1)).save(institutionUserIntf);
    }

    @Test
    void updateInstitutionUserIntf_WhenNotFound_ShouldThrowException() {
        UpdateInstitutionUserIntfRequestDTO requestDTO = new UpdateInstitutionUserIntfRequestDTO();
        requestDTO.setId(1L);

        when(institutionUserIntfRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionUserIntfService.updateInstitutionUserIntf(requestDTO));
        verify(institutionUserIntfRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionUserIntf_WhenDuplicate_ShouldThrowException() {
        UpdateInstitutionUserIntfRequestDTO requestDTO = new UpdateInstitutionUserIntfRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setScreenOrderNo(1);

        when(institutionUserIntfRepository.findById(requestDTO.getId())).thenReturn(Optional.of(new InstitutionUserIntf()));
        when(institutionUserIntfRepository.existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId(), requestDTO.getScreenOrderNo())).thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionUserIntfService.updateInstitutionUserIntf(requestDTO));
        verify(institutionUserIntfRepository, times(1)).findById(requestDTO.getId());
        verify(institutionUserIntfRepository, times(1)).existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId(), requestDTO.getScreenOrderNo());
    }

    @Test
    void updateInstitutionUserIntf_WhenValid_ShouldReturnDTO() throws MicroException {
        UpdateInstitutionUserIntfRequestDTO requestDTO = new UpdateInstitutionUserIntfRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setScreenOrderNo(1);
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        InstitutionUserIntfDTO existingInstitutionUserIntfDTO = new InstitutionUserIntfDTO();
        InstitutionUserIntf institutionUserIntf = new InstitutionUserIntf();

        when(institutionUserIntfRepository.findById(requestDTO.getId())).thenReturn(Optional.of(new InstitutionUserIntf()));
        when(institutionUserIntfRepository.existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId(), requestDTO.getScreenOrderNo())).thenReturn(false);
        when(institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId())).thenReturn(institutionDebtTypeDTO);
        when(institutionUserIntfMapper.toDTO(any())).thenReturn(existingInstitutionUserIntfDTO);
        when(institutionUserIntfMapper.toEntity(existingInstitutionUserIntfDTO)).thenReturn(institutionUserIntf);
        when(institutionUserIntfRepository.save(institutionUserIntf)).thenReturn(institutionUserIntf);
        when(institutionUserIntfMapper.toDTO(institutionUserIntf)).thenReturn(existingInstitutionUserIntfDTO);

        InstitutionUserIntfDTO result = adminInstitutionUserIntfService.updateInstitutionUserIntf(requestDTO);

        assertNotNull(result);
        verify(institutionUserIntfRepository, times(1)).findById(requestDTO.getId());
        verify(institutionUserIntfRepository, times(1)).existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId(), requestDTO.getScreenOrderNo());
        verify(institutionDebtTypeService, times(1)).getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        verify(institutionUserIntfRepository, times(1)).save(institutionUserIntf);
    }
}

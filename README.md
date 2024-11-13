package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminOwnerDepartmentService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProductService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.Institution;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.OwnerDepartmentDTO;
import com.ykb.payments.bill.transaction.institution.dto.ProductDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionRepository;
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

public class AdminInstitutionServiceImplTest {

    @InjectMocks
    private AdminInstitutionServiceImpl adminInstitutionService;

    @Mock
    private InstitutionRepository institutionRepository;

    @Mock
    private AdminInstitutionMapper institutionMapper;

    @Mock
    private AdminProductService productService;

    @Mock
    private AdminOwnerDepartmentService ownerDepartmentService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutions_ShouldReturnAllInstitutions() {
        List<Institution> institutionList = List.of(new Institution());
        when(institutionRepository.findAll()).thenReturn(institutionList);
        when(institutionMapper.toInstitutionDTOList(institutionList)).thenReturn(List.of(new InstitutionDTO()));

        List<InstitutionDTO> result = adminInstitutionService.getAllInstitutions();

        assertNotNull(result);
        assertEquals(1, result.size());
        verify(institutionRepository, times(1)).findAll();
        verify(institutionMapper, times(1)).toInstitutionDTOList(institutionList);
    }

    @Test
    void getInstitutionByIdTypeSecond_WhenFound_ShouldReturnDTO() {
        Institution institution = new Institution();
        when(institutionRepository.findById(1L)).thenReturn(Optional.of(institution));
        when(institutionMapper.toInstitutionDTO(institution)).thenReturn(new InstitutionDTO());

        InstitutionDTO result = adminInstitutionService.getInstitutionByIdTypeSecond(1L);

        assertNotNull(result);
        verify(institutionRepository, times(1)).findById(1L);
        verify(institutionMapper, times(1)).toInstitutionDTO(institution);
    }

    @Test
    void getInstitutionByIdTypeSecond_WhenNotFound_ShouldReturnNull() {
        when(institutionRepository.findById(1L)).thenReturn(Optional.empty());

        InstitutionDTO result = adminInstitutionService.getInstitutionByIdTypeSecond(1L);

        assertNull(result);
        verify(institutionRepository, times(1)).findById(1L);
    }

    @Test
    void createInstitution_WhenDuplicate_ShouldThrowException() {
        CreateInstitutionRequestDTO requestDTO = new CreateInstitutionRequestDTO();
        requestDTO.setProductCode("PROD123");
        requestDTO.setInstitutionCode("INST123");

        when(institutionRepository.findByProductCodeAndInstitutionCode(any(), any())).thenReturn(Optional.of(new Institution()));

        assertThrows(DataConflictException.class, () -> adminInstitutionService.createInstitution(requestDTO));
        verify(institutionRepository, times(1)).findByProductCodeAndInstitutionCode(any(), any());
    }

    @Test
    void createInstitution_WhenValid_ShouldReturnDTO() throws MicroException {
        CreateInstitutionRequestDTO requestDTO = new CreateInstitutionRequestDTO();
        requestDTO.setProductCode("PROD123");
        requestDTO.setInstitutionCode("INST123");
        ProductDTO productDTO = new ProductDTO();
        OwnerDepartmentDTO ownerDepartmentDTO = new OwnerDepartmentDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        Institution institution = new Institution();

        when(institutionRepository.findByProductCodeAndInstitutionCode(any(), any())).thenReturn(Optional.empty());
        when(productService.getProductByCode(requestDTO.getProductCode())).thenReturn(productDTO);
        when(ownerDepartmentService.getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode())).thenReturn(ownerDepartmentDTO);
        when(institutionMapper.toInstitutionDTO(requestDTO)).thenReturn(institutionDTO);
        when(institutionMapper.toInstitution(institutionDTO)).thenReturn(institution);
        when(institutionRepository.save(institution)).thenReturn(institution);
        when(institutionMapper.toInstitutionDTO(institution)).thenReturn(institutionDTO);

        InstitutionDTO result = adminInstitutionService.createInstitution(requestDTO);

        assertNotNull(result);
        verify(institutionRepository, times(1)).findByProductCodeAndInstitutionCode(any(), any());
        verify(productService, times(1)).getProductByCode(requestDTO.getProductCode());
        verify(ownerDepartmentService, times(1)).getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode());
        verify(institutionRepository, times(1)).save(institution);
    }

    @Test
    void updateInstitution_WhenNotFound_ShouldThrowException() {
        UpdateInstitutionRequestDTO requestDTO = new UpdateInstitutionRequestDTO();
        requestDTO.setId(1L);

        when(institutionRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionService.updateInstitution(requestDTO));
        verify(institutionRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitution_WhenValid_ShouldReturnDTO() throws MicroException {
        UpdateInstitutionRequestDTO requestDTO = new UpdateInstitutionRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setProductCode("PROD123");
        requestDTO.setInstitutionCode("INST123");
        ProductDTO productDTO = new ProductDTO();
        OwnerDepartmentDTO ownerDepartmentDTO = new OwnerDepartmentDTO();
        InstitutionDTO existingInstitutionDTO = new InstitutionDTO();
        Institution updatedInstitution = new Institution();

        when(institutionRepository.findById(requestDTO.getId())).thenReturn(Optional.of(new Institution()));
        when(institutionRepository.findByProductCodeAndInstitutionCode(requestDTO.getProductCode(), requestDTO.getInstitutionCode())).thenReturn(Optional.empty());
        when(productService.getProductByCode(requestDTO.getProductCode())).thenReturn(productDTO);
        when(ownerDepartmentService.getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode())).thenReturn(ownerDepartmentDTO);
        when(institutionMapper.toInstitutionDTO(any())).thenReturn(existingInstitutionDTO);
        when(institutionMapper.toInstitution(any())).thenReturn(updatedInstitution);
        when(institutionRepository.save(any())).thenReturn(updatedInstitution);
        when(institutionMapper.toInstitutionDTO(updatedInstitution)).thenReturn(existingInstitutionDTO);

        InstitutionDTO result = adminInstitutionService.updateInstitution(requestDTO);

        assertNotNull(result);
        verify(institutionRepository, times(1)).findById(requestDTO.getId());
        verify(institutionRepository, times(1)).findByProductCodeAndInstitutionCode(requestDTO.getProductCode(), requestDTO.getInstitutionCode());
        verify(productService, times(1)).getProductByCode(requestDTO.getProductCode());
        verify(ownerDepartmentService, times(1)).getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode());
        verify(institutionRepository, times(1)).save(updatedInstitution);
    }
}

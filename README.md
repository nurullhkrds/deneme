package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminPaymentMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionPymMethod;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.PaymentMethodDTO;
import com.ykb.payments.bill.transaction.institution.mapper.InstitutionPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionPymMethodRepository;
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

public class AdminInstitutionPymMethodServiceImplTest {

    @InjectMocks
    private AdminInstitutionPymMethodServiceImpl adminInstitutionPymMethodService;

    @Mock
    private InstitutionPymMethodRepository institutionPymMethodRepository;

    @Mock
    private InstitutionPymMethodMapper institutionPymMethodMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @Mock
    private AdminPaymentMethodService paymentMethodService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionPymMethods_ShouldReturnAllMethods() {
        List<InstitutionPymMethod> pymMethods = List.of(new InstitutionPymMethod());
        when(institutionPymMethodRepository.findAll()).thenReturn(pymMethods);
        when(institutionPymMethodMapper.toDTOList(pymMethods)).thenReturn(List.of(new InstitutionPymMethodDTO()));

        List<InstitutionPymMethodDTO> result = adminInstitutionPymMethodService.getAllInstitutionPymMethods();

        assertNotNull(result);
        assertEquals(1, result.size());
        verify(institutionPymMethodRepository, times(1)).findAll();
        verify(institutionPymMethodMapper, times(1)).toDTOList(pymMethods);
    }

    @Test
    void getInstitutionPymMethodById_WhenFound_ShouldReturnDTO() {
        InstitutionPymMethod pymMethod = new InstitutionPymMethod();
        when(institutionPymMethodRepository.findById(1L)).thenReturn(Optional.of(pymMethod));
        when(institutionPymMethodMapper.toDTO(pymMethod)).thenReturn(new InstitutionPymMethodDTO());

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.getInstitutionPymMethodById(1L);

        assertNotNull(result);
        verify(institutionPymMethodRepository, times(1)).findById(1L);
        verify(institutionPymMethodMapper, times(1)).toDTO(pymMethod);
    }

    @Test
    void getInstitutionPymMethodById_WhenNotFound_ShouldReturnNull() {
        when(institutionPymMethodRepository.findById(1L)).thenReturn(Optional.empty());

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.getInstitutionPymMethodById(1L);

        assertNull(result);
        verify(institutionPymMethodRepository, times(1)).findById(1L);
    }

    @Test
    void createInstitutionPymMethod_WhenDuplicate_ShouldThrowException() {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setPaymentMethod(new PaymentMethodDTO());

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(any(), any())).thenReturn(Optional.of(new InstitutionPymMethod()));

        assertThrows(DataConflictException.class, () -> adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO));
        verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(any(), any());
    }

    @Test
    void updateInstitutionPymMethod_WhenNotFound_ShouldThrowException() {
        UpdateInstitutionPymMethodRequestDTO requestDTO = new UpdateInstitutionPymMethodRequestDTO();
        requestDTO.setId(1L);

        when(institutionPymMethodRepository.findById(1L)).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionPymMethodService.updateInstitutionPymMethod(requestDTO));
        verify(institutionPymMethodRepository, times(1)).findById(1L);
    }

    @Test
    void createInstitutionPymMethod_WhenValid_ShouldReturnDTO() throws MicroException {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(1L);
        requestDTO.setPaymentMethod(EnumPaymentMethod.CARD);
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionPymMethodDTO pymMethodDTO = new InstitutionPymMethodDTO();
        pymMethodDTO.setInstitution(institutionDTO);  // Initialize InstitutionPymMethodDTO to avoid NullPointerException
        InstitutionPymMethod pymMethod = new InstitutionPymMethod();

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(any(), any())).thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(anyLong())).thenReturn(institutionDTO);
        when(paymentMethodService.getPaymentMethodByMethod(any())).thenReturn(paymentMethodDTO);
        when(institutionPymMethodMapper.toDTO(any())).thenReturn(pymMethodDTO);
        when(institutionPymMethodMapper.toInstitutionPymMethod(any())).thenReturn(pymMethod);
        when(institutionPymMethodRepository.save(any())).thenReturn(pymMethod);
        when(institutionPymMethodMapper.toDTO(pymMethod)).thenReturn(pymMethodDTO);

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO);

        assertNotNull(result);
        verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(any(), any());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(anyLong());
        verify(paymentMethodService, times(1)).getPaymentMethodByMethod(any());
        verify(institutionPymMethodRepository, times(1)).save(any());
    }
}

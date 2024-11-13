package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminOwnerDepartmentMapper;
import com.ykb.payments.bill.transaction.institution.domain.OwnerDepartment;
import com.ykb.payments.bill.transaction.institution.dto.OwnerDepartmentDTO;
import com.ykb.payments.bill.transaction.institution.repository.OwnerDepartmentRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class AdminOwnerDepartmentServiceTest {

    @InjectMocks
    private AdminOwnerDepartmentImpl adminOwnerDepartmentService;

    @Mock
    private OwnerDepartmentRepository ownerDepartmentRepository;

    @Mock
    private AdminOwnerDepartmentMapper ownerDepartmentMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getOwnerDepartmentByCode_WhenFound_ShouldReturnDTO() throws MicroException {
        String code = "DEPT123";
        OwnerDepartment ownerDepartment = new OwnerDepartment();
        OwnerDepartmentDTO ownerDepartmentDTO = new OwnerDepartmentDTO();

        when(ownerDepartmentRepository.findByCode(code)).thenReturn(ownerDepartment);
        when(ownerDepartmentMapper.toOwnerDepartmentDTO(ownerDepartment)).thenReturn(ownerDepartmentDTO);

        OwnerDepartmentDTO result = adminOwnerDepartmentService.getOwnerDepartmentByCode(code);

        assertNotNull(result);
        verify(ownerDepartmentRepository, times(1)).findByCode(code);
        verify(ownerDepartmentMapper, times(1)).toOwnerDepartmentDTO(ownerDepartment);
    }

    @Test
    void getOwnerDepartmentByCode_WhenNotFound_ShouldThrowException() {
        String code = "DEPT123";

        when(ownerDepartmentRepository.findByCode(code)).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminOwnerDepartmentService.getOwnerDepartmentByCode(code));
        verify(ownerDepartmentRepository, times(1)).findByCode(code);
    }
}

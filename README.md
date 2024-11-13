package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminProcessMapper;
import com.ykb.payments.bill.transaction.institution.dto.ProcessDTO;
import com.ykb.payments.bill.transaction.institution.domain.Process;
import com.ykb.payments.bill.transaction.institution.repository.ProcessRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class AdminProcessServiceTest {

    @InjectMocks
    private AdminProcessServiceImpl adminProcessService;

    @Mock
    private ProcessRepository processRepository;

    @Mock
    private AdminProcessMapper processMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getProcessByCode_WhenFound_ShouldReturnDTO() throws MicroException {
        String code = "PROCESS123";
        Process processEntity = new Process();
        ProcessDTO processDTO = new ProcessDTO();

        when(processRepository.findByCode(code)).thenReturn(processEntity);
        when(processMapper.toDTO(processEntity)).thenReturn(processDTO);

        ProcessDTO result = adminProcessService.getProcessByCode(code);

        assertNotNull(result);
        verify(processRepository, times(1)).findByCode(code);
        verify(processMapper, times(1)).toDTO(processEntity);
    }

    @Test
    void getProcessByCode_WhenNotFound_ShouldReturnNull() throws MicroException {
        String code = "PROCESS123";

        when(processRepository.findByCode(code)).thenReturn(null);

        ProcessDTO result = adminProcessService.getProcessByCode(code);

        assertNull(result);
        verify(processRepository, times(1)).findByCode(code);
    }
}

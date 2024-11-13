package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProcessService;
import com.ykb.payments.bill.transaction.institution.dto.ProcessDTO;
import com.ykb.payments.bill.transaction.institution.repository.ProcessRepository;
import org.springframework.stereotype.Service;

@Service
public class AdminProcessServiceImpl implements AdminProcessService {

    private final ProcessRepository processRepository;

    private final AdminProcessMapper processMapper;


    public AdminProcessServiceImpl(ProcessRepository processRepository, AdminProcessMapper processMapper) {
        this.processRepository = processRepository;
        this.processMapper = processMapper;
    }


    @Override
    public ProcessDTO getProcessByCode (String code) throws MicroException {
        return processMapper.toDTO(processRepository.findByCode(code));

    }
}

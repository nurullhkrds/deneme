package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminOwnerDepartmentMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminOwnerDepartmentService;
import com.ykb.payments.bill.transaction.institution.domain.OwnerDepartment;
import com.ykb.payments.bill.transaction.institution.dto.OwnerDepartmentDTO;
import com.ykb.payments.bill.transaction.institution.repository.OwnerDepartmentRepository;
import org.springframework.stereotype.Service;

@Service
public class AdminOwnerDepartmentImpl implements AdminOwnerDepartmentService {


    private final OwnerDepartmentRepository ownerDepartmentRepository;

    private final AdminOwnerDepartmentMapper ownerDepartmentMapper;

    public AdminOwnerDepartmentImpl(OwnerDepartmentRepository ownerDepartmentRepository, AdminOwnerDepartmentMapper ownerDepartmentMapper) {
        this.ownerDepartmentRepository = ownerDepartmentRepository;
        this.ownerDepartmentMapper = ownerDepartmentMapper;
    }



    @Override
    public OwnerDepartmentDTO getOwnerDepartmentByCode(String code) throws MicroException {
        OwnerDepartment entity= ownerDepartmentRepository.findByCode(code);
        if (entity == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.OWNER_DEPARTMENT_NOT_FOUND);
        }
        OwnerDepartmentDTO dto= ownerDepartmentMapper.toOwnerDepartmentDTO(entity);
        return dto;

    }
}

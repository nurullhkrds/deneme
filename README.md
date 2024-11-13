package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionUserIntfMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionUserIntfService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionUserIntfWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionUserIntf;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionUserIntfRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class AdminInstitutionUserIntfServiceImpl implements AdminInstitutionUserIntfService {

    private final InstitutionUserIntfRepository institutionUserIntfRepository;
    private final AdminInstitutionUserIntfMapper institutionUserIntfMapper;
    private final AdminInstitutionDebtTypeService institutionDebtTypeService;

    public AdminInstitutionUserIntfServiceImpl(InstitutionUserIntfRepository institutionUserIntfRepository, AdminInstitutionUserIntfMapper institutionUserIntfMapper, AdminInstitutionDebtTypeService institutionDebtTypeService) {
        this.institutionUserIntfRepository = institutionUserIntfRepository;
        this.institutionUserIntfMapper = institutionUserIntfMapper;
        this.institutionDebtTypeService = institutionDebtTypeService;
    }

    @Override
    public List<InstitutionUserIntfWebDTO> getAllInstitutionUserIntfs() {
        List<Object []> insUserIntfListOjectArray= institutionUserIntfRepository.findInstitutionUserIntfWithInstitution();

        return insUserIntfListOjectArray.stream()
                .map(institutionUserIntfMapper::objectArrayToWebDTO)
                .collect(Collectors.toList());

    }

    @Override
    public InstitutionUserIntfDTO getInstitutionUserIntfById(Long id) {

        InstitutionUserIntf institutionUserIntf = institutionUserIntfRepository.findById(id).orElse(null);
        if (institutionUserIntf != null) {
            return institutionUserIntfMapper.toDTO(institutionUserIntf);
        }
        return null;
    }

    @Override
    public InstitutionUserIntfDTO createInstitutionUserIntf(CreateInstitutionUserIntfRequestDTO requestDTO) throws MicroException {


        boolean existsByInstitutionDebtTypeIdAndScreenOrderNo = institutionUserIntfRepository
                .existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId()
                        , requestDTO.getScreenOrderNo());

        if (existsByInstitutionDebtTypeIdAndScreenOrderNo) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_USER_INTF);
        }

        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService
                .getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());


        if (institutionDebtTypeDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND);
        }

        InstitutionUserIntfDTO dto = institutionUserIntfMapper.toDTO(requestDTO);
        dto.setCreateDate(LocalDateTime.now());
        dto.setInstitutionDebtType(institutionDebtTypeDTO);

        InstitutionUserIntf institutionUserIntf = institutionUserIntfMapper.toEntity(dto);
        institutionUserIntf = institutionUserIntfRepository.save(institutionUserIntf);
        return institutionUserIntfMapper.toDTO(institutionUserIntf);
    }

    @Override
    public InstitutionUserIntfDTO updateInstitutionUserIntf(UpdateInstitutionUserIntfRequestDTO requestDTO) throws MicroException {

        InstitutionUserIntfDTO existingInstitutionUserIntfDTO = getInstitutionUserIntfById(requestDTO.getId());

        if (existingInstitutionUserIntfDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_USER_INTF_NOT_FOUND);
        }

        boolean existsByInstitutionDebtTypeIdAndScreenOrderNo = institutionUserIntfRepository
                .existsByInstitutionDebtTypeIdAndScreenOrderNo(requestDTO.getInstitutionDebtTypeId()
                        , requestDTO.getScreenOrderNo());

        if ((existsByInstitutionDebtTypeIdAndScreenOrderNo && !existingInstitutionUserIntfDTO.getId().equals(requestDTO.getId()))) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_USER_INTF);
        }

        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService
                .getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());

        if (institutionDebtTypeDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND);
        }


        existingInstitutionUserIntfDTO.setInstitutionDebtType(institutionDebtTypeDTO);
        existingInstitutionUserIntfDTO.setLabel(requestDTO.getLabel());
        existingInstitutionUserIntfDTO.setLabelDescription(requestDTO.getLabelDescription());
        existingInstitutionUserIntfDTO.setIsNumeric(requestDTO.getIsNumeric());
        existingInstitutionUserIntfDTO.setMaxLength(requestDTO.getMaxLength());
        existingInstitutionUserIntfDTO.setMinLength(requestDTO.getMinLength());
        existingInstitutionUserIntfDTO.setCompleteLengthFlag(requestDTO.getCompleteLengthFlag());
        existingInstitutionUserIntfDTO.setRegex(requestDTO.getRegex());
        existingInstitutionUserIntfDTO.setScreenOrderNo(requestDTO.getScreenOrderNo());
        existingInstitutionUserIntfDTO.setInterfaceType(requestDTO.getInterfaceType());
        existingInstitutionUserIntfDTO.setExplanation(requestDTO.getExplanation());
        existingInstitutionUserIntfDTO.setUpdatedBy(requestDTO.getUpdateUser());
        existingInstitutionUserIntfDTO.setUpdateDate(LocalDateTime.now());

        InstitutionUserIntf institutionUserIntf = institutionUserIntfMapper.toEntity(existingInstitutionUserIntfDTO);
        institutionUserIntf = institutionUserIntfRepository.save(institutionUserIntf);
        return institutionUserIntfMapper.toDTO(institutionUserIntf);
    }
}

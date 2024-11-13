package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionProcessService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProcessService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionProcess;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionProcessDTO;
import com.ykb.payments.bill.transaction.institution.dto.ProcessDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionProcessRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
public class AdminInstitutionProcessServiceImpl implements AdminInstitutionProcessService {

    private final InstitutionProcessRepository institutionProcessRepository;
    private final AdminInstitutionProcessMapper institutionProcessMapper;
    private final AdminInstitutionService institutionService;
    private final AdminProcessService processService;




    public AdminInstitutionProcessServiceImpl(InstitutionProcessRepository institutionProcessRepository, AdminInstitutionProcessMapper institutionProcessMapper, AdminInstitutionService institutionService, AdminProcessService processService) {
        this.institutionProcessRepository = institutionProcessRepository;
        this.institutionProcessMapper = institutionProcessMapper;
        this.institutionService = institutionService;
        this.processService = processService;
    }

    @Override
    public List<InstitutionProcessDTO> getAllInstitutionProcess() {
        List<InstitutionProcess> institutionProcessList=institutionProcessRepository.findAll();
        return institutionProcessMapper.toDTOList(institutionProcessList);
    }

    @Override
    public InstitutionProcessDTO getInstitutionProcessById(Long id) {
        Optional<InstitutionProcess> institutionProcess= institutionProcessRepository.findById(id);
        return institutionProcess.map(institutionProcessMapper::toDTO).orElse(null);
    }

    @Override
    public InstitutionProcessDTO createInstitutionProcess(CreateInstitutionProcessRequestDTO requestDTO) throws MicroException {

        boolean existsInstitutionProcess= institutionProcessRepository
                .existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(),requestDTO.getProcess());

        if(existsInstitutionProcess){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PROCESS);
        }


        InstitutionDTO institutionDTO=institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

        if (institutionDTO == null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }

        ProcessDTO processDTO=processService.getProcessByCode(requestDTO.getProcess());

        if (processDTO == null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.PROCESS_NOT_FOUND);
        }

        InstitutionProcessDTO dto= institutionProcessMapper.toDTO(requestDTO);
        dto.setProcess(processDTO);
        dto.setInstitution(institutionDTO);
        dto.setCreateDate(LocalDateTime.now());
        InstitutionProcess institutionProcess=institutionProcessMapper.toEntity(dto);
        institutionProcess=institutionProcessRepository.save(institutionProcess);
        return institutionProcessMapper.toDTO(institutionProcess);
    }

    @Override
    public InstitutionProcessDTO updateInstitutionProcess(UpdateInstitutionProcessRequestDTO requestDTO)
            throws MicroException {
        InstitutionProcessDTO institutionProcessDTO=getInstitutionProcessById(requestDTO.getId());

        if (institutionProcessDTO == null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_PROCESS_NOT_FOUND);
        }

        boolean existsInstitutionProcess= institutionProcessRepository
                .existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(),requestDTO.getProcess());

        if ((existsInstitutionProcess && !institutionProcessDTO.getId().equals(requestDTO.getId()))){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PROCESS);
        }

        InstitutionDTO institutionDTO=institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

        if (institutionDTO == null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }

        ProcessDTO processDTO=processService.getProcessByCode(requestDTO.getProcess());

        if (processDTO == null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.PROCESS_NOT_FOUND);
        }

        institutionProcessDTO.setInstitution(institutionDTO);
        institutionProcessDTO.setProcess(processDTO);
        institutionProcessDTO.setIsOnline(requestDTO.getIsOnline());
        institutionProcessDTO.setClassName(requestDTO.getClassName());
        institutionProcessDTO.setIsActive(requestDTO.getIsActive());
        institutionProcessDTO.setUpdateDate(LocalDateTime.now());
        institutionProcessDTO.setUpdatedBy(requestDTO.getUpdateUser());

        InstitutionProcess institutionProcess=institutionProcessMapper.toEntity(institutionProcessDTO);
        institutionProcess=institutionProcessRepository.save(institutionProcess);
        return institutionProcessMapper.toDTO(institutionProcess);

    }
}

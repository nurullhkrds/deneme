package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionPymMethodService;
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
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
public class AdminInstitutionPymMethodServiceImpl implements AdminInstitutionPymMethodService {

    private final InstitutionPymMethodRepository institutionPymMethodRepository;
    private final InstitutionPymMethodMapper institutionPymMethodMapper;
    private final AdminInstitutionService institutionService;
    private final AdminPaymentMethodService paymentMethodService;

    public AdminInstitutionPymMethodServiceImpl(InstitutionPymMethodRepository institutionPymMethodRepository, InstitutionPymMethodMapper institutionPymMethodMapper, AdminInstitutionService institutionService, AdminPaymentMethodService paymentMethodService) {
        this.institutionPymMethodRepository = institutionPymMethodRepository;
        this.institutionPymMethodMapper = institutionPymMethodMapper;
        this.institutionService = institutionService;
        this.paymentMethodService = paymentMethodService;
    }


    @Override
    public List<InstitutionPymMethodDTO> getAllInstitutionPymMethods() {
        List<InstitutionPymMethod> institutionPymMethods=institutionPymMethodRepository.findAll();
        return institutionPymMethodMapper.toDTOList(institutionPymMethods);
    }

    @Override
    public InstitutionPymMethodDTO getInstitutionPymMethodById(Long id) {
        InstitutionPymMethod institutionPymMethod=institutionPymMethodRepository.findById(id).orElse(null);
        if(institutionPymMethod!=null){
            return institutionPymMethodMapper.toDTO(institutionPymMethod);
        }

        return null;
    }

    @Override
    public InstitutionPymMethodDTO createInstitutionPymMethod(CreateInstitutionPymMethodRequestDTO requestDTO) throws MicroException {
        Optional<InstitutionPymMethod> existingInstitutionPymMethod= institutionPymMethodRepository
                .findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(),requestDTO.getPaymentMethod().getValue());

        if (existingInstitutionPymMethod.isPresent()){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PYM_METHOD);
        }

        InstitutionDTO institutionDTO= institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

        if (institutionDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }

        PaymentMethodDTO paymentMethodDTO= paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod());

        InstitutionPymMethodDTO institutionPymMethodDTO=institutionPymMethodMapper.toDTO(requestDTO);
        institutionPymMethodDTO.setInstitution(institutionDTO);
        institutionPymMethodDTO.setPaymentMethod(paymentMethodDTO);
        institutionPymMethodDTO.setCreateDate(LocalDateTime.now());
        InstitutionPymMethod institutionPymMethod=institutionPymMethodMapper.toInstitutionPymMethod(institutionPymMethodDTO);
        institutionPymMethod=institutionPymMethodRepository.save(institutionPymMethod);
        return institutionPymMethodMapper.toDTO(institutionPymMethod);
    }


    @Override
    public InstitutionPymMethodDTO updateInstitutionPymMethod(UpdateInstitutionPymMethodRequestDTO requestDTO) throws MicroException {

        InstitutionPymMethodDTO institutionPymMethodDTO = getInstitutionPymMethodById(requestDTO.getId());
        if (institutionPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_PYM_METHOD_NOT_FOUND);
        }

        Optional<InstitutionPymMethod> existingInstitutionPymMethod = institutionPymMethodRepository
                .findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue());

        if (existingInstitutionPymMethod.isPresent() && !existingInstitutionPymMethod.get().getId().equals(requestDTO.getId())) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PYM_METHOD);
        }

        InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

        if (institutionDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }

        PaymentMethodDTO paymentMethodDTO = paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod());

        institutionPymMethodDTO.setInstitution(institutionDTO);
        institutionPymMethodDTO.setPaymentMethod(paymentMethodDTO);
        institutionPymMethodDTO.setUpdateDate(LocalDateTime.now());
        institutionPymMethodDTO.setExpenseCode(requestDTO.getExpenseCode());
        institutionPymMethodDTO.setIsActive(requestDTO.getIsActive());
        institutionPymMethodDTO.setUpdatedBy(requestDTO.getUpdateUser());

        InstitutionPymMethod institutionPymMethod = institutionPymMethodMapper.toInstitutionPymMethod(institutionPymMethodDTO);
        institutionPymMethod = institutionPymMethodRepository.save(institutionPymMethod);

        return institutionPymMethodMapper.toDTO(institutionPymMethod);

    }



}

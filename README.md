package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdAccMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAccService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdAccWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChnnlPymMthdAcc;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChnnlPymMthdAccRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class AdminInstitutionChnlPymMthdAccServiceImpl implements AdminInstitutionChnlPymMthdAccService {

    private final InstitutionChnnlPymMthdAccRepository institutionChnnlPymMthdAccRepository;

    private final AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;

    private final AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;



    public AdminInstitutionChnlPymMthdAccServiceImpl(InstitutionChnnlPymMthdAccRepository institutionChnnlPymMthdAccRepository, AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper, AdminInstitutionChnlPymMethodService institutionChnlPymMethodService) {
        this.institutionChnnlPymMthdAccRepository = institutionChnnlPymMthdAccRepository;
        this.institutionChnnlPymMthdAccMapper = institutionChnnlPymMthdAccMapper;
        this.institutionChnlPymMethodService = institutionChnlPymMethodService;
    }

    @Override
    public List<InstitutionChnlPymMthdAccWebDTO> getAllInstitutionChannelPymMethodsAcc() {
        List<Object[]> results = institutionChnnlPymMthdAccRepository.findInstitutionChnlPymMthdAccWithInstitution();

        return results.stream()
                        .map(institutionChnnlPymMthdAccMapper::objectArrayToWebDTO)
                        .collect(Collectors.toList());
    }

    @Override
    public InstitutionChnnlPymMthdAccDTO getInstitutionChannelPymMethodAccById(Long id) {
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccRepository.findById(id).orElse(null);
        if (institutionChnnlPymMthdAcc != null) {
            return institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc);
        }
        return null;
    }

    @Override
    public InstitutionChnnlPymMthdAccDTO createInstitutionChannelPymMethodAcc(CreateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {

        boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
        boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

        if (existsByCurrency || existsByCollectionAccountNo){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_ACC);
        }
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO=institutionChnlPymMethodService
                .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

        if (institutionChannelPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }

        InstitutionChnnlPymMthdAccDTO dto = institutionChnnlPymMthdAccMapper.toDTO(requestDTO);
        dto.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);
        dto.setCreateDate(LocalDateTime.now());

        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccMapper.toInstitutionChnnlPymMthdAcc(dto);
        institutionChnnlPymMthdAcc=institutionChnnlPymMthdAccRepository.save(institutionChnnlPymMthdAcc);
        return institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc);
    }

    @Override
    public InstitutionChnnlPymMthdAccDTO updateInstitutionChannelPymMethodAcc(UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {

        InstitutionChnnlPymMthdAccDTO existingInstitutionChnnlPymMthdAccDTO = getInstitutionChannelPymMethodAccById(requestDTO.getId());

        if (existingInstitutionChnnlPymMthdAccDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHNNL_PYM_MTHD_ACC_NOT_FOUND);
        }

        boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
        boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

        if ((existsByCurrency && !existingInstitutionChnnlPymMthdAccDTO.getCurrency().equals(requestDTO.getCurrency()))
                || (existsByCollectionAccountNo && !existingInstitutionChnnlPymMthdAccDTO.getCollectionAccountNo().equals(requestDTO.getCollectionAccountNo()))) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_ACC);
        }

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = institutionChnlPymMethodService
                .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

        if (institutionChannelPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }

        existingInstitutionChnnlPymMthdAccDTO.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);
        existingInstitutionChnnlPymMthdAccDTO.setInstitutionAccountNo(requestDTO.getInstitutionAccountNo());
        existingInstitutionChnnlPymMthdAccDTO.setCollectionAccountNo(requestDTO.getCollectionAccountNo());
        existingInstitutionChnnlPymMthdAccDTO.setCurrency(requestDTO.getCurrency());
        existingInstitutionChnnlPymMthdAccDTO.setExpenseType(requestDTO.getExpenseType());
        existingInstitutionChnnlPymMthdAccDTO.setIsActive(requestDTO.getIsActive());
        existingInstitutionChnnlPymMthdAccDTO.setExpenseAccountNo(requestDTO.getExpenseAccountNo());
        existingInstitutionChnnlPymMthdAccDTO.setUpdateDate(LocalDateTime.now());
        existingInstitutionChnnlPymMthdAccDTO.setUpdatedBy(requestDTO.getUpdateUser());

        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccMapper.toInstitutionChnnlPymMthdAcc(existingInstitutionChnnlPymMthdAccDTO);
        institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccRepository.save(institutionChnnlPymMthdAcc);
        return institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc);
    }

}

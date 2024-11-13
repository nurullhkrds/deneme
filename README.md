package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdPscMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAPscService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdPscWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChnnlPymMthdPsc;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdPscDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChnnlPymMthdPscRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class AdminInstitutionChnlPymMthdPscServiceImpl implements AdminInstitutionChnlPymMthdAPscService {


    private final InstitutionChnnlPymMthdPscRepository institutionChnnlPymMthdPscRepository;

    private final AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper;
    private final AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;

    public AdminInstitutionChnlPymMthdPscServiceImpl(InstitutionChnnlPymMthdPscRepository institutionChnnlPymMthdPscRepository, AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper, AdminInstitutionChnlPymMethodService institutionChnlPymMethodService) {
        this.institutionChnnlPymMthdPscRepository = institutionChnnlPymMthdPscRepository;
        this.institutionChnnlPymMthdPscMapper = institutionChnnlPymMthdPscMapper;
        this.institutionChnlPymMethodService = institutionChnlPymMethodService;
    }


    @Override
    public List<InstitutionChnlPymMthdPscWebDTO> getAllInstitutionChnlPymMthdAPsc() {
        List<Object []> insChnnlPymMthdPscListOjectArray= institutionChnnlPymMthdPscRepository.findInstitutionChnlPymMthdAccWithInstitution();

        return insChnnlPymMthdPscListOjectArray.stream()
                .map(institutionChnnlPymMthdPscMapper::objectArrayToWebDTO)
                .collect(Collectors.toList());

    }

    @Override
    public InstitutionChnnlPymMthdPscDTO getInstitutionChnlPymMthdAPscById(Long id) {
        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPscDTO = institutionChnnlPymMthdPscRepository.findById(id).orElse(null);
        if (institutionChnnlPymMthdPscDTO != null) {
            return institutionChnnlPymMthdPscMapper.toDTO(institutionChnnlPymMthdPscDTO);
        }
        return null;
    }

    @Override
    public InstitutionChnnlPymMthdPscDTO createInstitutionChnlPymMthdPsc(CreateInstitutionChnlPymMthdPscRequestDTO requestDTO) throws MicroException {


        boolean existingInstitutionChnlPymMthdPscWithMthd= institutionChnnlPymMthdPscRepository.existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId());

        if (existingInstitutionChnlPymMthdPscWithMthd){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_PCC);
        }

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO=institutionChnlPymMethodService
                .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

        if (institutionChannelPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }

        InstitutionChnnlPymMthdPscDTO dto=institutionChnnlPymMthdPscMapper.toDTO(requestDTO);
        dto.setCreateDate(LocalDateTime.now());
        dto.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);

        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc=institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPsc(dto);
        institutionChnnlPymMthdPsc=institutionChnnlPymMthdPscRepository.save(institutionChnnlPymMthdPsc);


        return institutionChnnlPymMthdPscMapper.toDTO(institutionChnnlPymMthdPsc);
    }

    @Override
    public InstitutionChnnlPymMthdPscDTO updateInstitutionChnlPymMthdPsc(UpdateInstitutionChnlPymMthdPscRequestDTO requestDTO) throws MicroException {
        InstitutionChnnlPymMthdPscDTO existingInsChnnlPymMthdPscDTO = getInstitutionChnlPymMthdAPscById(requestDTO.getId());
        if (existingInsChnnlPymMthdPscDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHNNL_PYM_MTHD_PCC_NOT_FOUND);
        }

        boolean existingInstitutionChnlPymMthdPscWithMthd= institutionChnnlPymMthdPscRepository.existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId());

        if (existingInstitutionChnlPymMthdPscWithMthd && !(existingInsChnnlPymMthdPscDTO.getId().equals(requestDTO.getId()))){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_PCC);
        }

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO=institutionChnlPymMethodService
                .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

        if (institutionChannelPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }
        existingInsChnnlPymMthdPscDTO.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);
        existingInsChnnlPymMthdPscDTO.setUpdatedBy(requestDTO.getUpdateUser());
        existingInsChnnlPymMthdPscDTO.setMondayBlockDayCount(requestDTO.getMondayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setFridayBlockDayCount(requestDTO.getFridayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setSaturdayBlockDayCount(requestDTO.getSaturdayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setTuesdayBlockDayCount(requestDTO.getTuesdayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setWednesdayBlockDayCount(requestDTO.getWednesdayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setThursdayBlockDayCount(requestDTO.getThursdayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setSundayBlockDayCount(requestDTO.getSundayBlockDayCount());
        existingInsChnnlPymMthdPscDTO.setUpdateDate(LocalDateTime.now());

        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc=institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPsc(existingInsChnnlPymMthdPscDTO);
        institutionChnnlPymMthdPsc=institutionChnnlPymMthdPscRepository.save(institutionChnnlPymMthdPsc);

        return institutionChnnlPymMthdPscMapper.toDTO(institutionChnnlPymMthdPsc);
    }
}

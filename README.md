package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnlPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminPaymentMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChannelPymMethod;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.PaymentMethodDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChannelPymMethodRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;

@Service
public class AdminInstitutionChnlPymMethodServiceImpl implements AdminInstitutionChnlPymMethodService {

    private final InstitutionChannelPymMethodRepository institutionChannelPymMethodRepository;
    private final AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper;

    private final AdminInstitutionChannelService institutionChannelService;

    private final AdminPaymentMethodService paymentMethodService;

    public AdminInstitutionChnlPymMethodServiceImpl(InstitutionChannelPymMethodRepository institutionChannelPymMethodRepository, AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper, AdminInstitutionChannelService institutionChannelService, AdminPaymentMethodService paymentMethodService) {
        this.institutionChannelPymMethodRepository = institutionChannelPymMethodRepository;
        this.institutionChnlPymMethodMapper = institutionChnlPymMethodMapper;
        this.institutionChannelService = institutionChannelService;
        this.paymentMethodService = paymentMethodService;
    }

    @Override
    public List<InstitutionChannelPymMethodDTO> getAll() {
        List<InstitutionChannelPymMethod> institutionChannelPymMethodList=institutionChannelPymMethodRepository.findAll();
        return institutionChnlPymMethodMapper.toDTOList(institutionChannelPymMethodList);
    }

    @Override
    public InstitutionChannelPymMethodDTO getInstitutionChannelPymMethodById(Long id) {
        InstitutionChannelPymMethod institutionChannelPymMethod=institutionChannelPymMethodRepository.findById(id).orElse(null);
        if(institutionChannelPymMethod!=null){
            return institutionChnlPymMethodMapper.toDTO(institutionChannelPymMethod);
        }
        return null;
    }

    @Override
    public InstitutionChannelPymMethodDTO createInstitutionChannelPymMethod(CreateInstitutionChnlPymMethodRequestDTO requestDTO) throws MicroException {
        InstitutionChannelPymMethod existingInstitutionChannelPymMethod = institutionChannelPymMethodRepository
                .findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId()
                ,requestDTO.getPaymentMethod());

        if (existingInstitutionChannelPymMethod != null){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTON_CHANNEL_PYM_METHOD);
        }

        InstitutionChannelDTO institutionChannelDTO= institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId());

        if (institutionChannelDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_NOT_FOUND);
        }

        PaymentMethodDTO paymentMethodDTO = paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod());

        if (paymentMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.PAYMENT_METHOD_NOT_FOUND);

        }

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO=institutionChnlPymMethodMapper.toDTO(requestDTO);
        institutionChannelPymMethodDTO.setInstitutionChannel(institutionChannelDTO);
        institutionChannelPymMethodDTO.setPaymentMethod(paymentMethodDTO);
        institutionChannelDTO.setCreateDate(LocalDateTime.now());

        InstitutionChannelPymMethod institutionChannelPymMethod=
                institutionChnlPymMethodMapper.toInstitutionChannelPymMethod(institutionChannelPymMethodDTO);

        institutionChannelPymMethod= institutionChannelPymMethodRepository.save(institutionChannelPymMethod);
        return institutionChnlPymMethodMapper.toDTO(institutionChannelPymMethod);
    }

    @Override
    public InstitutionChannelPymMethodDTO updateInstitutionChannelPymMethod(UpdateInstitutionChnlPymMethodRequestDTO requestDTO) throws MicroException {
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = getInstitutionChannelPymMethodById(requestDTO.getId());
        if (institutionChannelPymMethodDTO == null){
             throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }

        InstitutionChannelPymMethod existingInstitutionChannelPymMethod = institutionChannelPymMethodRepository
                .findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId()
                        ,requestDTO.getPaymentMethod());

        if (existingInstitutionChannelPymMethod != null && !existingInstitutionChannelPymMethod.getId().equals(requestDTO.getId())){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTON_CHANNEL_PYM_METHOD);

        }

        InstitutionChannelDTO institutionChannelDTO= institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId());

        if (institutionChannelDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_NOT_FOUND);
        }

        PaymentMethodDTO paymentMethodDTO = paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod());

        if (paymentMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.PAYMENT_METHOD_NOT_FOUND);

        }
        institutionChannelPymMethodDTO.setPaymentMethod(paymentMethodDTO);
        institutionChannelPymMethodDTO.setInstitutionChannel(institutionChannelDTO);
        institutionChannelPymMethodDTO.setUpdateDate(LocalDateTime.now());
        institutionChannelPymMethodDTO.setUpdatedBy(requestDTO.getUpdateUser());
        institutionChannelPymMethodDTO.setIsActive(requestDTO.getIsActive());
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(requestDTO.getBlockDayStrategyCode());
        institutionChannelPymMethodDTO.setAccountingTemplateCode(requestDTO.getAccountingTemplateCode());
        institutionChannelPymMethodDTO.setBlockDayCount(requestDTO.getBlockDayCount());
        institutionChannelPymMethodDTO.setBlockDayType(requestDTO.getBlockDayType());
        institutionChannelPymMethodDTO.setProfitShareRate(requestDTO.getProfitShareRate());

        InstitutionChannelPymMethod institutionChannelPymMethod=
                institutionChnlPymMethodMapper.toInstitutionChannelPymMethod(institutionChannelPymMethodDTO);

        institutionChannelPymMethod= institutionChannelPymMethodRepository.save(institutionChannelPymMethod);
        return institutionChnlPymMethodMapper.toDTO(institutionChannelPymMethod);
    }
}

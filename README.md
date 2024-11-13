package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChannel;
import com.ykb.payments.bill.transaction.institution.dto.ChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChannelRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
public class AdminInstitutionChannelServiceImpl implements AdminInstitutionChannelService {

    private final InstitutionChannelRepository institutionChannelRepository;
    private final AdminInstitutionChannelMapper institutionChannelMapper;

    private final AdminInstitutionDebtTypeService institutionDebtTypeService;
    private final AdminChannelService channelService;

    public AdminInstitutionChannelServiceImpl(InstitutionChannelRepository institutionChannelRepository
            , AdminInstitutionChannelMapper institutionChannelMapper
            , AdminInstitutionDebtTypeService institutionDebtTypeService
            , AdminChannelService channelService) {
        this.institutionChannelRepository = institutionChannelRepository;
        this.institutionChannelMapper = institutionChannelMapper;
        this.institutionDebtTypeService = institutionDebtTypeService;
        this.channelService = channelService;
    }

    @Override
    public List<InstitutionChannelDTO> getAllInstitutionChannels() {
        List<InstitutionChannel> institutionChannels = institutionChannelRepository.findAll();
        return institutionChannelMapper.toDTOList(institutionChannels);
    }

    @Override
    public InstitutionChannelDTO getInstitutionChannelById(Long id) {
        InstitutionChannel institutionChannel = institutionChannelRepository.findById(id).orElse(null);
        return institutionChannelMapper.toDTO(institutionChannel);
    }

    @Override
    public InstitutionChannelDTO createInstitutionChannel(CreateInstitutionChannelRequestDTO requestDTO) throws MicroException {
        Optional<InstitutionChannel> existingInstitutionChannel = institutionChannelRepository
                .findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
        if (existingInstitutionChannel.isPresent()) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHANNEL);
        }

        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        if (institutionDebtTypeDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND);
        }


        ChannelDTO channelDTO = channelService.findChannelByChannelCode(requestDTO.getChannelCode());

        if (channelDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.CHANNEL_NOT_FOUND);
        }

        InstitutionChannelDTO institutionChannelDTO = institutionChannelMapper.toDTO(requestDTO);
        institutionChannelDTO.setChannel(channelDTO);
        institutionChannelDTO.setInstitutionDebtType(institutionDebtTypeDTO);
        institutionChannelDTO.setCreateDate(LocalDateTime.now());
        InstitutionChannel institutionChannel = institutionChannelMapper.toEntity(institutionChannelDTO);
        institutionChannel = institutionChannelRepository.save(institutionChannel);
        return institutionChannelMapper.toDTO(institutionChannel);

    }

    @Override
    public InstitutionChannelDTO updateInstitutionChannel(UpdateInstitutionChannelRequestDTO requestDTO) throws MicroException {
        InstitutionChannelDTO existingInstitutionChannelDTO = getInstitutionChannelById(requestDTO.getId());
        if (existingInstitutionChannelDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_NOT_FOUND);
        }

        Optional<InstitutionChannel> existingInstitutionChannelWithSameCode = institutionChannelRepository
                .findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
        if (existingInstitutionChannelWithSameCode.isPresent() && !existingInstitutionChannelWithSameCode.get().getId().equals(requestDTO.getId())) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHANNEL);
        }

        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        if (institutionDebtTypeDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND);
        }

        ChannelDTO channelDTO = channelService.findChannelByChannelCode(requestDTO.getChannelCode());
        if (channelDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.CHANNEL_NOT_FOUND);
        }

        existingInstitutionChannelDTO.setChannel(channelDTO);
        existingInstitutionChannelDTO.setInstitutionDebtType(institutionDebtTypeDTO);
        existingInstitutionChannelDTO.setIsNewBillNeeded(requestDTO.getIsNewBillNeeded());
        existingInstitutionChannelDTO.setIsOverPaymentAllowed(requestDTO.getIsOverPaymentAllowed());
        existingInstitutionChannelDTO.setIsPartialPaymentAllowed(requestDTO.getIsPartialPaymentAllowed());
        existingInstitutionChannelDTO.setWorkingStartTime(requestDTO.getWorkingStartTime());
        existingInstitutionChannelDTO.setWorkingFinishTime(requestDTO.getWorkingFinishTime());
        existingInstitutionChannelDTO.setIsActive(requestDTO.getIsActive());
        existingInstitutionChannelDTO.setUpdateDate(LocalDateTime.now());

        InstitutionChannel updatedInstitutionChannel = institutionChannelMapper.toEntity(existingInstitutionChannelDTO);
        updatedInstitutionChannel =institutionChannelRepository.save(updatedInstitutionChannel);


        return institutionChannelMapper.toDTO(updatedInstitutionChannel);
    }
}

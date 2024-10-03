INST_CHANNEL_PYM_METHOD_ID BUNA GÖRE UNİQUE OLACAK



 @Override
    public InstitutionChnnlPymMthdPscDTO createInstitutionChnlPymMthdPsc(CreateInstitutionChnlPymMthdPscRequestDTO requestDTO) throws MicroException {
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

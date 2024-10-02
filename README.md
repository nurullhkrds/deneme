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

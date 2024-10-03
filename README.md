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

        // Güncellenen kaydın kendisini hariç tutarak kontrol
        boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
        boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
                requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

        // Mevcut kaydın kendisini güncellemek için yukarıdaki kontroller eklendi
        if (existsByCurrency || existsByCollectionAccountNo){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_ACC);
        }

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = institutionChnlPymMethodService
                .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

        if (institutionChannelPymMethodDTO == null) {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
        }

        // Güncelleme işlemi
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

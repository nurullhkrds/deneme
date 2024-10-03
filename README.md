public InstitutionChnnlPymMthdAccDTO updateInstitutionChannelPymMethodAcc(UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {

    InstitutionChnnlPymMthdAccDTO existingInstitutionChnnlPymMthdAccDTO = getInstitutionChannelPymMethodAccById(requestDTO.getId());

    if (existingInstitutionChnnlPymMthdAccDTO == null) {
        throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHNNL_PYM_MTHD_ACC_NOT_FOUND);
    }

    // Mevcut kaydın kendisini hariç tutarak benzersizlik kontrolü
    boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
    boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

    // Güncellenen kaydın kendisiyle çakışmayı manuel kontrol et
    if ((existsByCurrency && !existingInstitutionChnnlPymMthdAccDTO.getCurrency().equals(requestDTO.getCurrency())) 
            || (existsByCollectionAccountNo && !existingInstitutionChnnlPymMthdAccDTO.getCollectionAccountNo().equals(requestDTO.getCollectionAccountNo()))) {
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

public InstitutionChnnlPymMthdAccDTO updateInstitutionChannelPymMethodAcc(UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {

    InstitutionChnnlPymMthdAccDTO existingInstitutionChnnlPymMthdAccDTO = getInstitutionChannelPymMethodAccById(requestDTO.getId());

    if (existingInstitutionChnnlPymMthdAccDTO == null) {
        throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHNNL_PYM_MTHD_ACC_NOT_FOUND);
    }

    // Kendi ID'si ile aynı olmayan başka bir kayıt olup olmadığını kontrol et
    InstitutionChnnlPymMthdAccDTO conflictByCurrency = institutionChnnlPymMthdAccRepository.findByInstitutionChannelPymMethodIdAndCurrency(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
    InstitutionChnnlPymMthdAccDTO conflictByCollectionAccountNo = institutionChnnlPymMthdAccRepository.findByInstitutionChannelPymMethodIdAndCollectionAccountNo(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

    if ((conflictByCurrency != null && !conflictByCurrency.getId().equals(requestDTO.getId())) ||
        (conflictByCollectionAccountNo != null && !conflictByCollectionAccountNo.getId().equals(requestDTO.getId()))) {
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

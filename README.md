@Override
public InstitutionChnnlPymMthdAccDTO updateInstitutionChannelPymMethodAcc(Long id, CreateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {
    // Mevcut kaydı bul
    InstitutionChnnlPymMthdAcc existingAcc = institutionChnnlPymMthdAccRepository.findById(id)
        .orElseThrow(() -> new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND));

    // Unique constraint kontrolü (güncelleme sırasında kendisiyle çakışmadığından emin olun)
    boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
    boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
            requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

    // Kendi kaydıyla çakışmasını engelle
    if ((existsByCurrency && !existingAcc.getCurrency().equals(requestDTO.getCurrency())) ||
        (existsByCollectionAccountNo && !existingAcc.getCollectionAccountNo().equals(requestDTO.getCollectionAccountNo()))) {
        throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHNNL_PYM_MTHD_ACC);
    }

    // İlişkili InstitutionChannelPymMethod kaydını kontrol et
    InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = institutionChnlPymMethodService
            .getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());

    if (institutionChannelPymMethodDTO == null) {
        throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PYM_METHOD_NOT_FOUND);
    }

    // Mevcut entity'i güncelle
    existingAcc.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);
    existingAcc.setCollectionAccountNo(requestDTO.getCollectionAccountNo());
    existingAcc.setInstitutionAccountNo(requestDTO.getInstitutionAccountNo());
    existingAcc.setCurrency(requestDTO.getCurrency());
    existingAcc.setExpenseType(requestDTO.getExpenseType());
    existingAcc.setExpenseAccountNo(requestDTO.getExpenseAccountNo());
    existingAcc.setIsActive(requestDTO.getIsActive());
    existingAcc.setUpdateDate(LocalDateTime.now());

    // Güncellenmiş entity'i kaydet
    institutionChnnlPymMthdAccRepository.save(existingAcc);

    return institutionChnnlPymMthdAccMapper.toDTO(existingAcc);
}

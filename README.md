 boolean existsByCurrency = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(
        requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
    boolean existsByCollectionAccountNo = institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCollectionAccountNo(
        requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCollectionAccountNo());

    if (existsByCurrency || existsByCollectionAccountNo) {
        throw new MicroException("Bu ödeme yöntemi ve para birimi ya da tahsilat hesap numarası zaten kayıtlı.");
    }

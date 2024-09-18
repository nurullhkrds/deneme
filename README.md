@Override
public DataResult<InstitutionFeatureDTO> updateInstitutionFeature(UpdateInstitutionFeatureRequest request) throws MicroException {
    // 1. Güncellenmek istenen InstitutionFeature'in mevcut olup olmadığını kontrol et
    InstitutionFeature existingInstitutionFeature = institutionFeatureRepository.findById(request.getId())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(404L);
            error.setErrorMessage(ResultConstant.INSTITUTION_FEATURE_NOT_FOUND.getMessage(request.getId()));
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // 2. Unique Constraint'i kontrol et (başka bir InstitutionFeature aynı Institution ve Feature kombinasyonu ile varsa hata ver)
    Optional<InstitutionFeature> duplicateFeature = institutionFeatureRepository
            .findByInstitutionIdAndFeatureCode(request.getInstitutionId(), request.getFeatureCode().getValue());
    if (duplicateFeature.isPresent() && !duplicateFeature.get().getId().equals(request.getId())) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.INSTITUTION_FEATURE_ALREADY_EXISTS.getMessage(request.getInstitutionId()));
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // 3. Institution ve Feature bilgilerini DTO olarak alıyoruz
    InstitutionDTO institutionDTO = institutionService.getInstitutionById(request.getInstitutionId());
    FeatureDTO featureDTO = featureService.getFeatureByCode(request.getFeatureCode());

    // 4. Mevcut InstitutionFeature entity'sini UpdateInstitutionFeatureRequest'ten gelen yeni verilerle güncelle
    InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeatureDTO(existingInstitutionFeature);
    institutionFeatureDTO.setInstitution(institutionDTO);  // DTO olarak güncelle
    institutionFeatureDTO.setFeature(featureDTO);  // DTO olarak güncelle
    institutionFeatureDTO.setFeatureValue(request.getFeatureValue());
    institutionFeatureDTO.setIsActive(request.getIsActive());

    // 5. DTO'dan entity'ye dönüşüm ve güncelleme
    InstitutionFeature updatedInstitutionFeature = institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO);
    updatedInstitutionFeature = institutionFeatureRepository.save(updatedInstitutionFeature);

    // 6. Güncellenen veriyi DTO'ya dönüştürüp geri döndürme
    InstitutionFeatureDTO updatedDTO = institutionFeatureMapper.toInstitutionFeatureDTO(updatedInstitutionFeature);
    return new SuccessDataResult<>(ResultConstant.INSTITUTION_FEATURE_UPDATED.getMessage(), updatedDTO, 200);
}

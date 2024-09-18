@Query(value = "SELECT * FROM INSTITUTION_FEATURE WHERE INSTITUTION_ID = :institutionId AND FEATURE_CODE = :featureCode", nativeQuery = true)
    Optional<InstitutionFeature> findByInstitutionIdAndFeatureCode(@Param("institutionId") Long institutionId, @Param("featureCode") String featureCode);

 Optional<InstitutionFeature> existingInstitutionFeature = institutionFeatureRepository
            .findByInstitutionIdAndFeatureCode(request.getInstitutionId(), request.getFeatureCode().toString());

    if (existingInstitutionFeature.isPresent()) {
        throw new DataConflictException("Bu Institution ve Feature kombinasyonu zaten mevcut");
    }

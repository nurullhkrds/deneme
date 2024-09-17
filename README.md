 Optional<Institution> existingInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (existingInstitution.isPresent()) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);  
        error.setErrorMessage("Product code and institution code combination already exists.");
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }
  INSTITUTION_UPDATED("Kurum başarıyla güncellendi"),
    INSTITUTION_DELETED("Kurum başarıyla silindi"),
    INSTITUTION_NOT_FOUND("Kurum bulunamadı"),

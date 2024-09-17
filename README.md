 Optional<Institution> existingInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (existingInstitution.isPresent()) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);  
        error.setErrorMessage("Product code and institution code combination already exists.");
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

@Override
public DataResult<InstitutionDTO> updateInstitution(UpdateInstitutionRequest request) throws MicroException {
    // Mevcut institution kontrolü
    Institution existingInstitution = institutionRepository.findById(request.getId())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(404L);
            error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // Duplicate institution kontrolü (Aynı productCode ve institutionCode ile başka bir kaydın olup olmadığını kontrol ediyoruz)
    Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(request.getId())) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // Product DTO ve OwnerDepartment DTO üzerinden kontrol
    ProductDTO productDTO = productRepository.findByCode(request.getProductCode())
        .map(productMapper::toProductDTO)
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.PRODUCT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentRepository.findByCode(request.getOwnerDepartmentCode())
        .map(ownerDepartmentMapper::toOwnerDepartmentDTO)
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // Mevcut institution güncelleniyor - Yeni DTO değerleriyle setleniyor
    institutionMapper.updateInstitutionFromDTO(request, existingInstitution);
    existingInstitution.setProduct(institutionMapper.toProductEntity(productDTO));
    existingInstitution.setOwnerDepartment(institutionMapper.toOwnerDepartmentEntity(ownerDepartmentDTO));

    // Güncellenmiş institution veritabanına kaydediliyor
    institutionRepository.save(existingInstitution);

    // Güncellenmiş entity'yi DTO'ya çeviriyoruz
    InstitutionDTO updatedDTO = institutionMapper.toInstitutionDTO(existingInstitution);

    return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), updatedDTO, 200);
}

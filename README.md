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

    // Duplicate institution kontrolü
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
        .map(product -> new ProductDTO(product.getCode(), product.getName()))
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.PRODUCT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentRepository.findByCode(request.getOwnerDepartmentCode())
        .map(department -> new OwnerDepartmentDTO(department.getCode(), department.getName()))
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // Request'ten DTO'ya MapStruct ile dönüşüm
    InstitutionDTO institutionDTO = institutionMapper.toInstitutionDTO(request);
    institutionDTO.setProduct(productDTO);
    institutionDTO.setOwnerDepartment(ownerDepartmentDTO);

    // DTO'dan entity'ye dönüşüm
    Institution updatedInstitution = institutionMapper.toInstitution(institutionDTO);
    updatedInstitution.setId(existingInstitution.getId());  // Güncelleme sırasında aynı ID'yi kullanıyoruz
    updatedInstitution.setCreateDate(existingInstitution.getCreateDate()); // Orijinal creation date'i koruyoruz
    updatedInstitution.setUpdateDate(LocalDateTime.now());  // Update date'i güncelliyoruz

    // Veritabanına kaydediliyor
    institutionRepository.save(updatedInstitution);

    // Güncellenmiş entity'yi DTO'ya çeviriyoruz
    InstitutionDTO updatedDTO = institutionMapper.toInstitutionDTO(updatedInstitution);

    return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), updatedDTO, 200);
}

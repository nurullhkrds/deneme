 // Mevcut Institution entity'sini DTO ile güncelleme (Product ve OwnerDepartment ile)
    @Mapping(target = "id", ignore = true)  // ID'yi değiştirmek istemiyoruz
    @Mapping(target = "createDate", ignore = true)  // createDate'i koruyoruz
    @Mapping(target = "updateDate", expression = "java(java.time.LocalDateTime.now())")  // updateDate'i güncelleyerek set ediyoruz
    @Mapping(source = "productCode", target = "product.code")  // Product DTO'yu set ediyoruz
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")  // OwnerDepartment DTO'yu set ediyoruz
    void updateInstitutionFromDTO(UpdateInstitutionRequest dto, @MappingTarget Institution institution);


@Override
public DataResult<InstitutionDTO> updateInstitution(UpdateInstitutionRequest request) throws MicroException {
    // 1. Mevcut institution kontrolü
    Institution existingInstitution = institutionRepository.findById(request.getId())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(404L);
            error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // 2. Duplicate institution kontrolü (Mevcut kayıt dışında benzersizlik kontrolü)
    Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(request.getId())) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // 3. Mevcut Institution kaydını DTO'dan güncelle (Product ve OwnerDepartment alanları ile birlikte)
    institutionMapper.updateInstitutionFromDTO(request, existingInstitution);  // DTO'dan mevcut kayda güncelleme yapılıyor

    // 4. Güncellenmiş institution veritabanına kaydediliyor
    institutionRepository.save(existingInstitution);

    // 5. Güncellenmiş entity'yi DTO'ya çevir ve geri döndür
    InstitutionDTO updatedDTO = institutionMapper.toInstitutionDTO(existingInstitution);

    return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), updatedDTO, 200);
}

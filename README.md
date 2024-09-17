@Mapper(componentModel = "spring")
public interface InstitutionMapper {

    // Create ve Update Request'ten DTO'ya dönüşüm
    @Mapping(source = "productCode", target = "product.code")
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")
    @Mapping(source = "createUser", target = "createdBy")
    InstitutionDTO toInstitutionDTO(CreateInstitutionRequest request);

    @Mapping(source = "productCode", target = "product.code")
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")
    @Mapping(source = "updateUser", target = "updatedBy")
    @Mapping(source = "id", target = "id")  // ID'yi de DTO'ya ekliyoruz
    InstitutionDTO toInstitutionDTO(UpdateInstitutionRequest request);

    // DTO'dan Entity'ye dönüşüm
    Institution toInstitution(InstitutionDTO dto);

    // Entity'den DTO'ya dönüşüm
    InstitutionDTO toInstitutionDTO(Institution institution);

    // ProductDTO'dan Product entity'ye dönüşüm
    Product toProductEntity(ProductDTO productDTO);

    // OwnerDepartmentDTO'dan OwnerDepartment entity'ye dönüşüm
    OwnerDepartment toOwnerDepartmentEntity(OwnerDepartmentDTO ownerDepartmentDTO);

    // Entity'den ProductDTO'ya dönüşüm
    ProductDTO toProductDTO(Product product);

    // Entity'den OwnerDepartmentDTO'ya dönüşüm
    OwnerDepartmentDTO toOwnerDepartmentDTO(OwnerDepartment ownerDepartment);

    // Mevcut Institution entity'sini DTO ile güncelleme
    @Mapping(target = "id", ignore = true)  // ID'yi değiştirmek istemiyoruz
    @Mapping(target = "createDate", ignore = true)  // createDate'i koruyoruz
    @Mapping(target = "updateDate", expression = "java(java.time.LocalDateTime.now())")  // updateDate'i güncelleyerek set ediyoruz
    void updateInstitutionFromDTO(UpdateInstitutionRequest dto, @MappingTarget Institution institution);
}
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

    // 3. Product ve OwnerDepartment DTO'ları ile kontrol ve setleme
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

    // 4. Mevcut Institution kaydını DTO'dan güncelle
    institutionMapper.updateInstitutionFromDTO(request, existingInstitution);  // DTO'dan mevcut kayda güncelleme yapılıyor
    existingInstitution.setProduct(institutionMapper.toProductEntity(productDTO));  // Product DTO'dan entity'ye dönüşüm ve setleme
    existingInstitution.setOwnerDepartment(institutionMapper.toOwnerDepartmentEntity(ownerDepartmentDTO));  // OwnerDepartment DTO'dan entity'ye dönüşüm ve setleme
    existingInstitution.setUpdateDate(LocalDateTime.now());  // Update tarihi güncelleniyor

    // 5. Güncellenmiş institution veritabanına kaydediliyor
    institutionRepository.save(existingInstitution);

    // 6. Güncellenmiş entity'yi DTO'ya çevir ve geri döndür
    InstitutionDTO updatedDTO = institutionMapper.toInstitutionDTO(existingInstitution);

    return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), updatedDTO, 200);
}

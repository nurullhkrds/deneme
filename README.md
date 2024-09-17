// CreateInstitutionRequest'ten InstitutionDTO'ya dönüştürme
    @Mapping(source = "productCode", target = "product.code")
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")
    InstitutionDTO toInstitutionDTO(CreateInstitutionRequest request);

    // DTO'dan Entity'ye dönüştürme
    Institution toInstitution(InstitutionDTO dto);

    // Entity'den DTO'ya dönüşüm
    InstitutionDTO toInstitutionDTO(Institution institution);

    List<InstitutionDTO> toInstitutionDTOList(List<Institution> institutions);


@Override
public DataResult<InstitutionDTO> createInstitution(CreateInstitutionRequest request) throws MicroException {
    // Benzersizlik kontrolü
    Optional<Institution> existingInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (existingInstitution.isPresent()) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // Product ve OwnerDepartment DTO'su üzerinden kontrol
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

    // Request'ten InstitutionDTO'ya MapStruct ile dönüştürme
    InstitutionDTO institutionDTO = institutionMapper.toInstitutionDTO(request);
    institutionDTO.setProduct(productDTO);
    institutionDTO.setOwnerDepartment(ownerDepartmentDTO);

    // DTO'dan entity'ye dönüşüm
    Institution institution = institutionMapper.toInstitution(institutionDTO);
    institution.setCreateDate(LocalDateTime.now());

    // Veritabanına kaydediliyor
    institutionRepository.save(institution);

    // Kaydedilen entity DTO'ya çevriliyor
    InstitutionDTO savedInstitutionDTO = institutionMapper.toInstitutionDTO(institution);

    return new SuccessDataResult<>(ResultConstant.INSTITUTION_CREATED.getMessage(), savedInstitutionDTO, 200);
}

@Getter
@Setter
public class UpdateInstitutionRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution to update", example = "1", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long id;

    @NotNull
    @Size(min = 1, max = 50)
    @Schema(description = "Institution code", example = "INST001", requiredMode = Schema.RequiredMode.REQUIRED)
    private String institutionCode;

    @NotNull
    @Schema(description = "Product code", example = "PRODUCT123", requiredMode = Schema.RequiredMode.REQUIRED)
    private String productCode;

    @NotNull
    @Schema(description = "Customer number", example = "123456789", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long customerNo;

    @NotNull
    @Size(min = 1, max = 100)
    @Schema(description = "Name of the institution", example = "My Institution", requiredMode = Schema.RequiredMode.REQUIRED)
    private String name;

    @NotNull
    @Size(min = 1, max = 500)
    @Schema(description = "Explanation about the institution", example = "This is a description.", requiredMode = Schema.RequiredMode.REQUIRED)
    private String explanation;

    @NotNull
    @Schema(description = "Owner department code", example = "DEP001", requiredMode = Schema.RequiredMode.REQUIRED)
    private String ownerDepartmentCode;

    @Schema(description = "Protocol start date", example = "2024-01-01")
    private LocalDate protocolStartDate;

    @Schema(description = "Protocol end date", example = "2024-12-31")
    private LocalDate protocolEndDate;

    @NotNull
    @Schema(description = "Is reverse allowed", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isReverseAllowed;

    @NotNull
    @Schema(description = "Is order allowed", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isOrderAllowed;

    @NotNull
    @Schema(description = "Has debt type", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean hasDebtType;

    @Size(max = 1000)
    @Schema(description = "Icon text", example = "Institution Icon")
    private String iconText;

    @NotNull
    @Schema(description = "Is active", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;
}
@Override
public DataResult<AdapterInstitutionDTO> updateInstitution(UpdateInstitutionRequest request) throws MicroException {
    // 1. Güncellenecek kurumun var olup olmadığını kontrol ediyoruz
    Institution existingInstitution = institutionRepository.findById(request.getId())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(404L);
            error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // 2. Kurum kodu ve ürün koduna göre başka bir kayıt var mı kontrolü (Güncellemede, mevcut kurum dışında bir kayıt olmamalı)
    Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(request.getId())) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // 3. Güncelleme işlemi için Product ve OwnerDepartment kontrolü
    Product product = productRepository.findByCode(request.getProductCode())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.PRODUCT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    OwnerDepartment ownerDepartment = ownerDepartmentRepository.findByCode(request.getOwnerDepartmentCode())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // 4. Kurum bilgilerini güncelliyoruz
    existingInstitution.setProduct(product);
    existingInstitution.setInstitutionCode(request.getInstitutionCode());
    existingInstitution.setCustomerNo(request.getCustomerNo());
    existingInstitution.setName(request.getName());
    existingInstitution.setExplanation(request.getExplanation());
    existingInstitution.setOwnerDepartment(ownerDepartment);
    existingInstitution.setProtocolStartDate(request.getProtocolStartDate());
    existingInstitution.setProtocolEndDate(request.getProtocolEndDate());
    existingInstitution.setIsReverseAllowed(request.getIsReverseAllowed());
    existingInstitution.setIsOrderAllowed(request.getIsOrderAllowed());
    existingInstitution.setHasDebtType(request.getHasDebtType());
    existingInstitution.setIconText(request.getIconText());
    existingInstitution.setIsActive(request.getIsActive());
    existingInstitution.setUpdateDate(LocalDateTime.now());

    // 5. Güncellenmiş kurum bilgisini kaydediyoruz
    institutionRepository.save(existingInstitution);

    // 6. Güncellenmiş veriyi DTO'ya dönüştürüyoruz
    AdapterInstitutionDTO dto = adapterInstitutionMapper.toInstitutionDTO(existingInstitution);

    // 7. Başarı mesajı ve DTO'yu dönüyoruz
    return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), dto, 200);
}




   @PutMapping("/updateInstitution")
    public ResponseEntity<DataResult<AdapterInstitutionDTO>> updateInstitution(@RequestBody UpdateInstitutionRequest request) throws MicroException {
        DataResult<AdapterInstitutionDTO> result = adapterInstitutionService.updateInstitution(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

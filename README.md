@Override
public DataResult<InstitutionDebtTypeWebDTO> updateInstitutionDebtType(UpdateInstitutionDebtTypeRequest request) throws MicroException {
    // 1. Güncellenmek istenen InstitutionDebtType kaydının olup olmadığını kontrol et
    InstitutionDebtType existingDebtType = institutionDebtTypeRepository.findById(request.getId())
        .orElseThrow(() -> {
            ExceptionData error = new ExceptionData();
            error.setErrorCode(404L);
            error.setErrorMessage(ResultConstant.INSTITUTION_DEBT_TYPE_NOT_FOUND.getMessage());
            error.setApplicationName(SERVICE_NAME);
            return new DataNotFoundException(error);
        });

    // 2. Aynı institution ve debt type koduna sahip başka bir kayıt var mı kontrol et
    Optional<InstitutionDebtType> duplicateDebtType = institutionDebtTypeRepository
            .findByInstitutionIdAndDebtTypeCode(request.getInstitutionId(), request.getDebtType());
    if (duplicateDebtType.isPresent() && !duplicateDebtType.get().getId().equals(request.getId())) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.INSTITUTION_DEBT_TYPE_ALREADY_EXISTS.getMessage());
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }

    // 3. Institution verisini al
    InstitutionDTO institutionDTO = institutionService.getInstitutionById(request.getInstitutionId());
    if (institutionDTO == null) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(404L);
        error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
        error.setApplicationName(SERVICE_NAME);
        throw new DataNotFoundException(error);
    }

    // 4. Mevcut entity'yi güncellemek için request'ten gelen verileri DTO'ya setle
    InstitutionDebtTypeDTO dto = institutionDebtTypeMapper.toDTO(request);
    dto.setInstitution(institutionDTO);
    dto.setUpdateDate(LocalDateTime.now());

    // 5. DTO'dan entity'ye dönüşüm yaparak kaydı güncelle
    InstitutionDebtType updatedDebtType = institutionDebtTypeMapper.toEntity(dto);
    updatedDebtType = institutionDebtTypeRepository.save(updatedDebtType);

    // 6. Güncellenen entity'yi webDTO'ya çevirip geri döndür
    InstitutionDebtTypeWebDTO webDTO = institutionDebtTypeMapper.toWebDTO(updatedDebtType);
    return new SuccessDataResult<>(ResultConstant.INSTITUTION_DEBT_TYPE_UPDATED.getMessage(), webDTO, 200);
}

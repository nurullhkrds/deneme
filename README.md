@Override
	public DataResult<InstitutionDTO> updateInstitution(UpdateInstitutionRequest request) throws MicroException {
		Institution existingInstitution = institutionRepository.findById(request.getId())
				.orElseThrow(() -> {
					ExceptionData error = new ExceptionData();
					error.setErrorCode(404L);
					error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
					error.setApplicationName(SERVICE_NAME);
					return new DataNotFoundException(error);
				});

		Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
		if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(request.getId())) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(409L);
			error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
			error.setApplicationName(SERVICE_NAME);
			throw new DataConflictException(error);
		}

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

		institutionRepository.save(existingInstitution);

		InstitutionDTO dto = institutionMapper.toInstitutionDTO(existingInstitution);

		return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), dto, 200);
	}

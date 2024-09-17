@Override
	public DataResult<InstitutionDTO> createInstitution(CreateInstitutionRequest request) throws MicroException {
		Optional<Institution> existingInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
		if (existingInstitution.isPresent()) {
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

		Institution institution = institutionMapper.toInstitution(request);

		institution.setProduct(product);
		institution.setOwnerDepartment(ownerDepartment);
		institution.setCreateDate(LocalDateTime.now());
		institutionRepository.save(institution);
		InstitutionDTO dto = institutionMapper.toInstitutionDTO(institution);

		return new SuccessDataResult<>(ResultConstant.INSTITUTION_CREATED.getMessage(), dto, 200);
	}

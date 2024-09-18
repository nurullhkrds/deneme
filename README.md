	@Override
	public DataResult<InstitutionDebtTypeWebDTO> createInstitutionDebtType(CreateInstitutionDebtTypeRequest request) throws MicroException {
		Optional<InstitutionDebtType> existingInstitutionDebtType = InstitutionDebtTypeRepository
				.findByInstitutionIdAndDebtTypeCode(request.getInstitutionId(), request.getDebtType());
		if (existingInstitutionDebtType.isPresent()) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(409L);
			error.setErrorMessage(ResultConstant.INSTITUTION_DEBT_TYPE_ALREADY_EXISTS.getMessage());
			error.setApplicationName(SERVICE_NAME);
			throw new DataConflictException(error);
		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionById(request.getInstitutionId());

		if(institutionDTO == null) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(404L);
			error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
			error.setApplicationName(SERVICE_NAME);
			throw new DataNotFoundException(error);
		}

		InstitutionDebtTypeDTO dto = InstitutionDebtTypeMapper.toDTO(request);
		dto.setInstitution(institutionDTO);
		dto.setCreateDate(LocalDateTime.now());

		InstitutionDebtType entity = InstitutionDebtTypeMapper.toEntity(dto);
		entity = InstitutionDebtTypeRepository.save(entity);

		InstitutionDebtTypeWebDTO webDTO = InstitutionDebtTypeMapper.toWebDTO(entity);
		return new SuccessDataResult<>(ResultConstant.INSTITUTION_DEBT_TYPE_CREATED.getMessage(), webDTO,200);
		
	}

	@Override
	public DataResult<InstitutionDebtTypeWebDTO> updateInstitutionDebtType(UpdateInstitutionDebtTypeRequest request) throws MicroException {
		
		
		return null;
	}

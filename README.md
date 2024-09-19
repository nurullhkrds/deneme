	@Override
	public DataResult<InstitutionWebDTO> updateInstitution(UpdateInstitutionRequestDTO requestDTO) throws MicroException {

		InstitutionDTO existingInstitutionDTO = getInstitutionById(requestDTO.getId());
		if (existingInstitutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
		}

		Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(requestDTO.getProductCode(), requestDTO.getInstitutionCode());
		if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(requestDTO.getId())) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PRODUCT);

		}

		ProductDTO productDTO = productService.getProductByCode(requestDTO.getProductCode());
		OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentService.getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode());
		
		existingInstitutionDTO.setInstitutionCode(requestDTO.getInstitutionCode());
		existingInstitutionDTO.setProduct(productDTO);
		existingInstitutionDTO.setCustomerNo(requestDTO.getCustomerNo());
		existingInstitutionDTO.setName(requestDTO.getName());
		existingInstitutionDTO.setExplanation(requestDTO.getExplanation());
		existingInstitutionDTO.setOwnerDepartment(ownerDepartmentDTO);
		existingInstitutionDTO.setProtocolStartDate(requestDTO.getProtocolStartDate());
		existingInstitutionDTO.setProtocolEndDate(requestDTO.getProtocolEndDate());
		existingInstitutionDTO.setIsReverseAllowed(requestDTO.getIsReverseAllowed());
		existingInstitutionDTO.setIsOrderAllowed(requestDTO.getIsOrderAllowed());
		existingInstitutionDTO.setHasDebtType(requestDTO.getHasDebtType());
		existingInstitutionDTO.setIconText(requestDTO.getIconText());
		existingInstitutionDTO.setIsActive(requestDTO.getIsActive());
		
		existingInstitutionDTO.setUpdateDate(LocalDateTime.now());
		existingInstitutionDTO.setUpdatedBy(requestDTO.getUpdateUser());
		
		

		Institution updatedInstitution = institutionMapper.toInstitution(existingInstitutionDTO);
		institutionRepository.save(updatedInstitution);

		InstitutionWebDTO response = institutionMapper.toInstitutionWebDTO(updatedInstitution);

		return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), response, 200);
	}

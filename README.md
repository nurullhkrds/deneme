@Override
	public DataResult<InstitutionFeatureDTO> createInstitutionFeature(CreateInstitutionFeatureRequest request) throws MicroException {

		InstitutionDTO institutionDTO = institutionService.getInstitutionById(request.getInstitutionId());
		FeatureDTO featureDTO = featureService.getFeatureByCode(request.getFeatureCode());
		InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeature(request);
		institutionFeatureDTO.setInstitution(institutionDTO);
		institutionFeatureDTO.setFeature(featureDTO);

		InstitutionFeature institutionFeature = institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO);
		institutionFeature = institutionFeatureRepository.save(institutionFeature);
		institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature);
		return new SuccessDataResult<>(ResultConstant.INSTITUTION_FEATURE_CREATED.getMessage(), institutionFeatureDTO,200);
	}

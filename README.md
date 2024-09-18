	@Override
	public DataResult<InstitutionFeatureDTO> createInstitutionFeature(CreateInstitutionFeatureRequest request) throws MicroException {

		Optional<InstitutionFeature> existingInstitutionFeature = institutionFeatureRepository
				.findByInstitutionIdAndFeatureCode(request.getInstitutionId(), request.getFeatureCode().getValue());
		if (existingInstitutionFeature.isPresent()) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(409L);
			error.setErrorMessage(ResultConstant.INSTITUTION_FEATURE_ALREADY_EXISTS.getMessage(request.getInstitutionId()));
			error.setApplicationName(SERVICE_NAME);
			throw new DataNotFoundException(error);
		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionById(request.getInstitutionId());

		if (institutionDTO == null) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(404L);
			error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
			error.setApplicationName(SERVICE_NAME);
			throw new DataNotFoundException(error);
		}

		FeatureDTO featureDTO = featureService.getFeatureByCode(request.getFeatureCode());
		InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeature(request);
		institutionFeatureDTO.setInstitution(institutionDTO);
		institutionFeatureDTO.setFeature(featureDTO);

		InstitutionFeature institutionFeature = institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO);
		institutionFeature = institutionFeatureRepository.save(institutionFeature);
		institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature);
		return new SuccessDataResult<>(ResultConstant.INSTITUTION_FEATURE_CREATED.getMessage(), institutionFeatureDTO,200);
	}



@Override
	public DataResult<InstitutionFeatureDTO> updateInstitutionFeature(UpdateInstitutionFeatureRequest request) {


		return null;
	}
@Getter
@Setter
public class UpdateInstitutionFeatureRequest extends BaseUpdateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution feature", example = "1", required = true)
    private Long id;

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;

    @NotNull
    @Schema(description = "Enum Code of the feature", example = "DAILY_LIMITATIONS_CHECK_FOR_INSTITUTION", required = true)
    private EnumFeatureCode featureCode;

    @Size(max = 250)
    @Schema(description = "Value of the feature", example = "Some feature value")
    private String featureValue;

    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;
}

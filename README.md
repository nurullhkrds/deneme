  @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionByReturnMapCode(String returnMapCode) throws MicroException {
        Optional<ReturnMapDefinition> optionalReturnMapDefinition = returnMapDefinitionRepository.findByReturnMapCode(returnMapCode);

        if (optionalReturnMapDefinition.isPresent()) {
            ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(optionalReturnMapDefinition.get());

            List<String> institutions = returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode);

            if (dto != null) {
                dto.setInstitutions(institutions);
                return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto, 200);
            } else {
                throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.CONVERSION_FAILED);
            }
        } else {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.DATA_NOT_FOUND);
        }
    }

bu definition parametresi burada bağlı kurumlarıda çekiyorum..





 @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        if (returnMapDTOList.isEmpty()) {
            return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
        }

        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }


@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class ReturnMapDTO  {

	private Long id;
	private ReturnMapDefinitionDTO returnMapDefinition;
	private String returnMapCode;
	private String institutionReturnCode;
	private String institutionReturnText;
	private String bankReturnCode;
	private String bankReturnText;
	private String returnType;
	private Boolean isReversible;

}
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class ReturnMapDefinitionDTO {
    private Long id;
    private String returnMapCode;

    private Boolean isActive;
    private List<String> institutions;

}

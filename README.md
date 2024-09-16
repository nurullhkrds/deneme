  
public class ReturnMapService implements IReturnMapService {
    private final ReturnMapRepository returnMapRepository;
    private final ReturnMapMapper returnMapMapper;
    private final IReturnMapDefinitionService returnMapDefinitionService;


 @Transactional
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) throws DataNotFoundException, DataConflictException {
        DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());
        if (request.getReturnMapDefinitionId() != null && !definitionDataResult.isSuccess()) {
            throw new DataNotFoundException(Long.valueOf(ResultConstant.RECORD_NOT_FOUND.getMessage()));
        }

        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

        if (existingReturnMap.isPresent()) {
            throw  new DataConflictException(Long.valueOf(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage())) ;
        }
        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setCreatedBy(request.getCreateUser());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS : EnumReturnType.ERROR);

        ReturnMap result = returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto = returnMapMapper.toReturnMapDTO(result);

        if (resultDto != null) {
            return new SuccessDataResult<>("ReturnMap Added", resultDto, 200);
        }
        return new ErrorDataResult<>("ReturnMap not be added", null, 400);
    }


   @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) throws DataConflictException, DataNotFoundException {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
        DataResult<ReturnMapDefinition> definitionDataResult = returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

        if (request.getReturnMapDefinitionId() != null && !definitionDataResult.isSuccess()) {
            throw new DataNotFoundException(Long.valueOf(ResultConstant.RECORD_NOT_FOUND.getMessage()));
        }

        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());

        if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
            throw  new DataConflictException(Long.valueOf(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage())) ;
        }

        ReturnMap returnMap = returnMapDataResult.getData();
        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setUpdatedBy(request.getUpdateUser());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS : EnumReturnType.ERROR);
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());

        ReturnMap result = returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto = returnMapMapper.toReturnMapDTO(result);

        if (resultDto != null) {
            return new SuccessDataResult<>("ReturnMap updated", resultDto, 200);
        }
        return new ErrorDataResult<>("ReturnMap not be updated", null, 400);
    }

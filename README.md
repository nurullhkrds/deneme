@Service
public class ReturnMapDefinitionService implements IReturnMapDefinitionService {

    private final ReturnMapDefinitionRepository returnMapDefinitionRepository;

    private final ReturnMapDefinitionMapper returnMapDefinitionMapper;

    public ReturnMapDefinitionService(ReturnMapDefinitionRepository returnMapDefinitionRepository, ReturnMapDefinitionMapper returnMapDefinitionMapper) {
        this.returnMapDefinitionRepository = returnMapDefinitionRepository;
        this.returnMapDefinitionMapper = returnMapDefinitionMapper;
    }



    @Override
    public DataResult<List<ReturnMapDefinitionDTO>> getAllReturnMapDefinitionReturnMap() {
        List<ReturnMapDefinition> definitionList= returnMapDefinitionRepository.findAll();
        List<ReturnMapDefinitionDTO> dtoList=returnMapDefinitionMapper.toReturnMapDefinitionDTOList(definitionList);
        return new SuccessDataResult<>(ResultConstant.DATA_LISTED.getMessage(),dtoList,200);
    }
    @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionByReturnMapCode(String returnMapCode) {
        Optional<ReturnMapDefinition> optionalReturnMapDefinition = returnMapDefinitionRepository.findByReturnMapCode(returnMapCode);

        if (optionalReturnMapDefinition.isPresent()) {
            ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(optionalReturnMapDefinition.get());

            List<String> institutions = returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode);

            if (dto != null) {
                dto.setInstitutions(institutions);
                return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto, 200);
            } else {
                return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
            }
        } else {
            return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 200);
        }
    }


    @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionById(Long id) {
        Optional<ReturnMapDefinition> returnMapDefinitionOptional=returnMapDefinitionRepository.findById(id);

        if (returnMapDefinitionOptional.isPresent()){
            ReturnMapDefinitionDTO dto= returnMapDefinitionMapper.toReturnMapDefinitionDTO(returnMapDefinitionOptional.get());
            return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto,200);
        }
        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
    }

    @Override
    public DataResult<ReturnMapDefinition> getReturnMapDefinitionByIdForServices(Long id) {
        Optional<ReturnMapDefinition> returnMapDefinitionOptional=returnMapDefinitionRepository.findById(id);
        return returnMapDefinitionOptional.<DataResult<ReturnMapDefinition>>map(returnMapDefinition -> new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), returnMapDefinition, 200)).orElse(null);
    }

    @Override
    public DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(CreateReturnMapDefinitionRequest request) {

        Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent()) {
            return new ErrorDataResult<>(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), null, 400);
        }

        ReturnMapDefinition newReturnMap = new ReturnMapDefinition();
        newReturnMap.setReturnMapCode(request.getReturnMapCode());
        newReturnMap.setIsActive(request.getIsActive());

        ReturnMapDefinition savedData = returnMapDefinitionRepository.save(newReturnMap);

        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedData);

        if (dto == null) {
            return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
        }

        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), dto,200);
    }

    @Override
    public DataResult<ReturnMapDefinitionDTO> updateReturnMapDefinition(UpdateReturnMapDefinitionRequest request) {

        DataResult<ReturnMapDefinition> returnMapDefinitionDataResult = getReturnMapDefinitionByIdForServices(request.getId());

        if (returnMapDefinitionDataResult.isSuccess()) {
            ReturnMapDefinition changedData = returnMapDefinitionDataResult.getData();
            Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

            if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
                return new ErrorDataResult<>(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), null, 400);
            }

            changedData.setIsActive(request.getIsActive());
            changedData.setReturnMapCode(request.getReturnMapCode());

            ReturnMapDefinition updatedData = returnMapDefinitionRepository.save(changedData);
            ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(updatedData);

            if (dto == null) {
                return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
            }

            return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), dto, 200);
        }

        return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 400);
    }


    @Override
    public Result deleteReturnMapDefinitions(DeleteIdsRequest request) {
        try{
            List<ReturnMapDefinition> returnMapsToDelete = returnMapDefinitionRepository.findAllById(request.getIds());
            returnMapDefinitionRepository.deleteAll(returnMapsToDelete);
            return new SuccessResult(ResultConstant.SUCCESSFULLY_DELETED.getMessage(), 200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
        }
    }




}

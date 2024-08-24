@Service
@RequiredArgsConstructor
public class ReturnMapService implements IReturnMapService {
    private final ReturnMapRepository returnMapRepository;
    private final ReturnMapMapper returnMapMapper;
    private final IReturnMapDefinitionService returnMapDefinitionService;



    @Override
    public DataResult<List<ReturnMapDTO>> getAll() {
        List<ReturnMap> returnMapList=returnMapRepository.findAll();
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);
        boolean success = !returnMapDTOList.isEmpty();
        String message = success ? "Data retrieved successfully" : "No data found";
        int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

        return new DataResult<>(success, message, returnMapDTOList, statusCode);    }

    @Override
    public DataResult<List<ReturnMapDTO>> getAllByReturnMapCode(String returnMapCode) {
        List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        boolean success = !returnMapDTOList.isEmpty();
        String message = success ? "Data retrieved successfully" : "No data found";
        int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

        return new DataResult<>(success, message, returnMapDTOList, statusCode);
    }

    @Transactional
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
       DataResult<ReturnMapDefinition> definitionDataResult=returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());
       if (request.getReturnMapDefinitionId()!=null && !definitionDataResult.isSuccess()){
           return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
       }

        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);
        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap Added",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be added",null,400);
    }

    @Override
    public DataResult<ReturnMap> getReturnMapByIdSecond(Long returnMapCodeId) {
        Optional<ReturnMap> returnMapOptional=returnMapRepository.findById(returnMapCodeId);
        if (returnMapOptional.isPresent()){
            return new SuccessDataResult<>("result found",returnMapOptional.get(),200 );
        }
        return new ErrorDataResult<>("result not found !",null,400);    }

    @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());
        DataResult<ReturnMapDefinition> definitionDataResult=returnMapDefinitionService.getReturnMapDefinitionByIdForServices(request.getReturnMapDefinitionId());

        if (request.getReturnMapDefinitionId()!=null && !definitionDataResult.isSuccess()){
            return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null,400);
        }

        if (!returnMapDataResult.isSuccess()){
            return new ErrorDataResult<>("ReturnMap not be updated",null,400);
        }


        ReturnMap returnMap = returnMapDataResult.getData();

        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnMapDefinition(definitionDataResult.getData());

        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);

        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap updated",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be updated",null,400);
    }



    @Transactional
    public Result deleteReturnMaps(DeleteIdsRequest request) {
        try{
            List<ReturnMap> returnMapsToDelete = returnMapRepository.findAllById(request.getIds());
            returnMapRepository.deleteAll(returnMapsToDelete);
            return new SuccessResult("ReturnMaps deleted",200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
        }
    }


    @Transactional
    public Result copyReturnMaps(CopyForIdsAndDataRequest request) {
        try {
            List<ReturnMap> returnMapsToCopy = returnMapRepository.findAllById(request.getIds());
            List<ReturnMap> copiedReturnMaps = new ArrayList<>();

            for (ReturnMap original : returnMapsToCopy) {
                ReturnMap copied = new ReturnMap();
                copied.setReturnMapCode(request.getReturnMapCode());
                copied.setInstitutionReturnCode(original.getInstitutionReturnCode());
                copied.setInstitutionReturnText(original.getInstitutionReturnText());
                copied.setBankReturnCode(original.getBankReturnCode());
                copied.setBankReturnText(original.getBankReturnText());
                copied.setReturnMapDefinition(original.getReturnMapDefinition());
                copied.setReturnType(original.getReturnType());
                copied.setIsReversible(original.getIsReversible());
                copiedReturnMaps.add(copied);
            }

            returnMapRepository.saveAll(copiedReturnMaps);
            return new SuccessResult("copy successful", 200);

        } catch (Exception e) {
            return new ErrorResult("Error! " + e.getMessage(), 400);
        }
    }


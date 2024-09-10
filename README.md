@Service
public class ReturnMapDefinitionService implements IReturnMapDefinitionService {

    private final ReturnMapDefinitionRepository returnMapDefinitionRepository;

    private final ReturnMapDefinitionMapper returnMapDefinitionMapper;


 @Override
    public DataResult<List<ReturnMapDefinitionDTO>> getAllReturnMapDefinitionReturnMapWithIsActiveTrue() {
        List<ReturnMapDefinition> definitionList=returnMapDefinitionRepository.findAllByIsActive(true);
        List<ReturnMapDefinitionDTO> dtoList=returnMapDefinitionMapper.toReturnMapDefinitionDTOList(definitionList);
        return new SuccessDataResult<>(ResultConstant.DATA_LISTED.getMessage(),dtoList,200);

    } 
    buna bir unit test yaz 

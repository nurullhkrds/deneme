Description:

Parameter 1 of constructor in com.ykb.payments.bill.transaction.adapter.services.concretes.ReturnMapDefinitionService required a bean of type 'com.ykb.payments.bill.transaction.adapter.mapper.ReturnMapDefinitionMapper' that could not be found.


Action:

Consider defining a bean of type 'com.ykb.payments.bill.transaction.adapter.mapper.ReturnMapDefinitionMapper' in your configuration.


Process finished with exit code 1




@Mapper(componentModel = "spring")
public interface ReturnMapDefinitionMapper {


    ReturnMapDefinitionDTO toReturnMapDefinitionDTO(ReturnMapDefinition entity);

    ReturnMapDefinition toReturnMapDefinition(ReturnMapDefinitionDTO dto);

    List<ReturnMapDefinitionDTO> toReturnMapDefinitionDTOList(List<ReturnMapDefinition> entityList);


}





@Service
public class ReturnMapDefinitionService implements IReturnMapDefinitionService {

    private final ReturnMapDefinitionRepository returnMapDefinitionRepository;

    private final ReturnMapDefinitionMapper returnMapDefinitionMapper;

    public ReturnMapDefinitionService(ReturnMapDefinitionRepository returnMapDefinitionRepository, ReturnMapDefinitionMapper returnMapDefinitionMapper) {
        this.returnMapDefinitionRepository = returnMapDefinitionRepository;
        this.returnMapDefinitionMapper = returnMapDefinitionMapper;
    }

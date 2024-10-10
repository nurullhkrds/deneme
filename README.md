@Mapper(componentModel = "spring")
public interface ReturnMapMapper {

	ReturnMapDTO toReturnMapDTO(ReturnMap entity);
	
	ReturnMap toReturnMap(ReturnMapDTO dto);
	
	List<ReturnMapDTO> toReturnMapDTOList(List<ReturnMap> entityList);



}

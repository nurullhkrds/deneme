@Mapper(componentModel = "spring")
public interface InstitutionOrderPymGroupMapper {
    InstitutionOrderPymGroupMapper INSTANCE = Mappers.getMapper(InstitutionOrderPymGroupMapper.class);

    InstitutionOrderPymGroupDTO toInstitutionOrderPymGroupDTO(InstitutionOrderPymGroup institutionOrderPymGroup);

    InstitutionOrderPymGroup toInstitutionOrderPymGroup(InstitutionOrderPymGroupDTO institutionOrderPymGroupDTO);
}

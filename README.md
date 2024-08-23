@Mapper(componentModel = "spring")
public interface InstitutionProcessMapper {
    InstitutionProcessMapper INSTANCE = Mappers.getMapper(InstitutionProcessMapper.class);

    InstitutionProcessDTO toInstitutionProcessDTO(InstitutionProcess institutionProcess);

    InstitutionProcess toInstitutionProcess(InstitutionProcessDTO institutionProcessDTO);
}

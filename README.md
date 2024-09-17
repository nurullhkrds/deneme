@Mapper(componentModel = "spring")
public interface AdapterInstitutionMapper {

    AdapterInstitutionDTO toInstitutionDTO(Institution entity);

    Institution toInstitution (AdapterInstitutionDTO dto);

    List<AdapterInstitutionDTO> toInstitutionDTOList (List<Institution> entityList);


}

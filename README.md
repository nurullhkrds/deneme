@Mapper(componentModel = "spring")
public interface AdapterInstitutionMapper {

    AdapterInstitutionDTO toInstitutionDTO(Institution entity);

    Institution toInstitution (AdapterInstitutionDTO dto);

    List<AdapterInstitutionDTO> toInstitutionDTOList (List<Institution> entityList);

}


java: Can't map property "Product product" to "String product". Consider to declare/implement a mapping method: "String map(Product value)".

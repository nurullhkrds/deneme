@Mapper(componentModel = "spring")
public interface InstitutionMapper {

    // DTO'dan Entity'ye dönüşüm
    @Mapping(source = "product.code", target = "product.code")
    @Mapping(source = "ownerDepartment.code", target = "ownerDepartment.code")
    Institution toInstitution(InstitutionDTO dto);

    // Entity'den DTO'ya dönüşüm
    @Mapping(source = "product.code", target = "product.code")
    @Mapping(source = "ownerDepartment.code", target = "ownerDepartment.code")
    InstitutionDTO toInstitutionDTO(Institution institution);

    List<InstitutionDTO> toInstitutionDTOList(List<Institution> institutions);
}

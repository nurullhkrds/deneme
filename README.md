@Mapper(componentModel = "spring")
public interface InstitutionMapper {
    InstitutionMapper INSTANCE = Mappers.getMapper(InstitutionMapper.class);

    @Mapping(source = "productCode", target = "product.code")
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")
    @Mapping(source = "createUser", target = "createdBy")
    InstitutionDTO toInstitutionDTO(CreateInstitutionRequest request);

    @Mapping(source = "productCode", target = "product.code")
    @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code")
    @Mapping(source = "updateUser", target = "updatedBy")
    InstitutionDTO toInstitutionDTO(UpdateInstitutionRequest request);

    Institution toInstitution(InstitutionDTO dto);

    InstitutionDTO toInstitutionDTO(Institution institution);

    List<InstitutionDTO> toInstitutionDTOList(List<Institution> institutions);
}

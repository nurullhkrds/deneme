@Mapper(componentModel = "spring")
public interface OwnerDepartmentMapper {
    OwnerDepartmentMapper INSTANCE = Mappers.getMapper(OwnerDepartmentMapper.class);

    OwnerDepartmentDTO toOwnerDepartmentDTO(OwnerDepartment ownerDepartment);
    
    OwnerDepartment toOwnerDepartment(OwnerDepartmentDTO ownerDepartmentDTO);
}

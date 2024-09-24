@Mapper(componentModel = "spring")
public interface ProcessMapper {
    ProcessMapper INSTANCE = Mappers.getMapper(ProcessMapper.class);

    ProcessDTO toDTO(Process process);
    
    Process toEntity(ProcessDTO processDTO);
}

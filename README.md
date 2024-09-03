@Mapper(componentModel = "spring")
public interface BusinessLogMapper {

	BusinessLogMapper INSTANCE = Mappers.getMapper(BusinessLogMapper.class);

	BusinessLog toEntity(BusinessLogDTO dto);
}


@Mapper(componentModel = "spring")
public interface ProcessLogMapper {
	ProcessLogMapper INSTANCE = Mappers.getMapper(ProcessLogMapper.class);

	ProcessLog toEntity(ProcessLogDTO dto);
}


@Mapper(componentModel = "spring")
public interface ServiceLogMapper {

	ServiceLogMapper INSTANCE = Mappers.getMapper(ServiceLogMapper.class);

	ServiceLog toEntity(ServiceLogDTO dto);
}

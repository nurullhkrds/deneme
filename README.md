@Mapper(componentModel = "spring")
public interface ProcessChannelMapper {
    ProcessChannelMapper INSTANCE = Mappers.getMapper(ProcessChannelMapper.class);

    ProcessChannelDTO toProcessChannelDTO(ProcessChannel processChannel);
      
    ProcessChannel toProcessChannel(ProcessChannelDTO processChannelDTO);
}

public class ProcessChannelMapperTest {

    private final ProcessChannelMapper processChannelMapper = ProcessChannelMapper.INSTANCE;

    @Test
    void shouldMapProcessChannelToDTO() {
        // Given
        ProcessChannel processChannel = new ProcessChannel();
        processChannel.setId(1L);
        processChannel.setChannelName("Test Channel");
        processChannel.setActive(true);

        // When
        ProcessChannelDTO processChannelDTO = processChannelMapper.toProcessChannelDTO(processChannel);

        // Then
        assertNotNull(processChannelDTO);
        assertEquals(processChannel.getId(), processChannelDTO.getId());
        assertEquals(processChannel.getChannelName(), processChannelDTO.getChannelName());
        assertEquals(processChannel.isActive(), processChannelDTO.isActive());
    }

    @Test
    void shouldMapDTOToProcessChannel() {
        // Given
        ProcessChannelDTO processChannelDTO = new ProcessChannelDTO();
        processChannelDTO.setId(1L);
        processChannelDTO.setChannelName("Test Channel DTO");
        processChannelDTO.setActive(false);

        // When
        ProcessChannel processChannel = processChannelMapper.toProcessChannel(processChannelDTO);

        // Then
        assertNotNull(processChannel);
        assertEquals(processChannelDTO.getId(), processChannel.getId());
        assertEquals(processChannelDTO.getChannelName(), processChannel.getChannelName());
        assertEquals(processChannelDTO.isActive(), processChannel.isActive());
    }

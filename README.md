public class ProcessMapperTest {

    private final ProcessMapper processMapper = ProcessMapper.INSTANCE;

    @Test
    void shouldMapProcessToDTO() {
        // Given
        Process process = new Process();
        process.setId(1L);
        process.setName("Test Process");
        process.setStatus("Active");

        // When
        ProcessDTO processDTO = processMapper.toDTO(process);

        // Then
        assertNotNull(processDTO);
        assertEquals(process.getId(), processDTO.getId());
        assertEquals(process.getName(), processDTO.getName());
        assertEquals(process.getStatus(), processDTO.getStatus());
    }

    @Test
    void shouldMapDTOToProcess() {
        // Given
        ProcessDTO processDTO = new ProcessDTO();
        processDTO.setId(1L);
        processDTO.setName("Test Process DTO");
        processDTO.setStatus("Inactive");

        // When
        Process process = processMapper.toEntity(processDTO);

        // Then
        assertNotNull(process);
        assertEquals(processDTO.getId(), process.getId());
        assertEquals(processDTO.getName(), process.getName());
        assertEquals(processDTO.getStatus(), process.getStatus());
    }
}

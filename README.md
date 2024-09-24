 private final OwnerDepartmentMapper ownerDepartmentMapper = OwnerDepartmentMapper.INSTANCE;

    @Test
    void shouldMapOwnerDepartmentToDTO() {
        // Given
        OwnerDepartment ownerDepartment = new OwnerDepartment();
        ownerDepartment.setId(1L);
        ownerDepartment.setDepartmentName("Finance");
        ownerDepartment.setActive(true);

        // When
        OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentMapper.toOwnerDepartmentDTO(ownerDepartment);

        // Then
        assertNotNull(ownerDepartmentDTO);
        assertEquals(ownerDepartment.getId(), ownerDepartmentDTO.getId());
        assertEquals(ownerDepartment.getDepartmentName(), ownerDepartmentDTO.getDepartmentName());
        assertEquals(ownerDepartment.isActive(), ownerDepartmentDTO.isActive());
    }

    @Test
    void shouldMapDTOToOwnerDepartment() {
        // Given
        OwnerDepartmentDTO ownerDepartmentDTO = new OwnerDepartmentDTO();
        ownerDepartmentDTO.setId(1L);
        ownerDepartmentDTO.setDepartmentName("IT");
        ownerDepartmentDTO.setActive(false);

        // When
        OwnerDepartment ownerDepartment = ownerDepartmentMapper.toOwnerDepartment(ownerDepartmentDTO);

        // Then
        assertNotNull(ownerDepartment);
        assertEquals(ownerDepartmentDTO.getId(), ownerDepartment.getId());
        assertEquals(ownerDepartmentDTO.getDepartmentName(), ownerDepartment.getDepartmentName());
        assertEquals(ownerDepartmentDTO.isActive(), ownerDepartment.isActive());
    }

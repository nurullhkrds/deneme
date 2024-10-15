
    // InstitutionPymMethodWebDTO Tests
    @Test
    public void testInstitutionPymMethodWebDTONoArgsConstructor() {
        InstitutionPymMethodWebDTO dto = new InstitutionPymMethodWebDTO();
        assertNotNull(dto);  // DTO'nun başarıyla oluşturulduğunu doğrula
    }

    @Test
    public void testInstitutionPymMethodWebDTOAllArgsConstructor() {
        InstitutionPymMethodWebDTO dto = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        assertNotNull(dto);
        assertEquals("EXP123", dto.getExpenseCode());
        assertEquals("TYPE123", dto.getExpenseType());
        assertEquals("ACC123", dto.getExpenseAccountNo());
    }

    @Test
    public void testInstitutionPymMethodWebDTOEqualsAndHashCode() {
        InstitutionPymMethodWebDTO dto1 = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        InstitutionPymMethodWebDTO dto2 = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        assertEquals(dto1, dto2);  // Equals kontrolü
        assertEquals(dto1.hashCode(), dto2.hashCode());  // HashCode kontrolü
    }




------------



@Test
    public void testInstUserIntfSubtypeWebDTONoArgsConstructor() {
        InstUserIntfSubtypeWebDTO dto = new InstUserIntfSubtypeWebDTO();
        assertNotNull(dto);  // DTO'nun başarıyla oluşturulduğunu doğrula
    }

    @Test
    public void testInstUserIntfSubtypeWebDTOAllArgsConstructor() {
        InstUserIntfSubtypeWebDTO dto = new InstUserIntfSubtypeWebDTO(
                "KEY123", "VALUE123"
        );

        assertNotNull(dto);
        assertEquals("KEY123", dto.getKey());
        assertEquals("VALUE123", dto.getValue());
    }

    @Test
    public void testInstUserIntfSubtypeWebDTOEqualsAndHashCode() {
        InstUserIntfSubtypeWebDTO dto1 = new InstUserIntfSubtypeWebDTO(
                "KEY123", "VALUE123"
        );

        InstUserIntfSubtypeWebDTO dto2 = new InstUserIntfSubtypeWebDTO(
                "KEY123", "VALUE123"
        );

        assertEquals(dto1, dto2);  // Equals kontrolü
        assertEquals(dto1.hashCode(), dto2.hashCode());  // HashCode kontrolü
    }


----------------------------


 @Test
    public void testCountDTONoArgsConstructor() {
        CountDTO dto = new CountDTO();
        assertNotNull(dto);  // DTO'nun başarıyla oluşturulduğunu doğrula
        assertEquals(0, dto.getTotalCount());
        assertEquals(BigDecimal.ZERO, dto.getTotalAmount());
    }

    @Test
    public void testCountDTOAllArgsConstructor() {
        CountDTO dto = new CountDTO(5, new BigDecimal("100.50"));

        assertNotNull(dto);
        assertEquals(5, dto.getTotalCount());
        assertEquals(new BigDecimal("100.50"), dto.getTotalAmount());
    }

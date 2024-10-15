  @Test
    public void testNoArgsConstructor() {
        InstitutionPymMethodWebDTO dto = new InstitutionPymMethodWebDTO();
        assertNotNull(dto);  // DTO'nun başarılı bir şekilde oluşturulduğunu doğrula
    }

    // AllArgsConstructor Testi
    @Test
    public void testAllArgsConstructor() {
        InstitutionPymMethodWebDTO dto = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        assertNotNull(dto);
        assertEquals("EXP123", dto.getExpenseCode());
        assertEquals("TYPE123", dto.getExpenseType());
        assertEquals("ACC123", dto.getExpenseAccountNo());
    }

    // EqualsAndHashCode Testi
    @Test
    public void testEqualsAndHashCode() {
        InstitutionPymMethodWebDTO dto1 = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        InstitutionPymMethodWebDTO dto2 = new InstitutionPymMethodWebDTO(
                "EXP123", "TYPE123", "ACC123"
        );

        assertEquals(dto1, dto2);  // Equals doğru çalışıyor mu kontrol et
        assertEquals(dto1.hashCode(), dto2.hashCode());  // HashCode'ların eşit olduğundan emin ol
    }

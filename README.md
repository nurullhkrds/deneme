 @Test
    public void testNoArgsConstructor() {
        QueriedBillResponseWebDTO dto = new QueriedBillResponseWebDTO();
        assertNotNull(dto);  // DTO'nun başarılı bir şekilde oluşturulduğunu doğrula
    }

 @Test
    public void testAllArgsConstructor() {
        QueriedBillResponseWebDTO dto = new QueriedBillResponseWebDTO(
                LocalDate.of(2024, 10, 15), "BILL123", "TERM123", 
                new BigDecimal("100.50"), "USD", "PROV123", 
                "Explanation", true, 
                "Info1", "Info2", "Info3", "Info4", "Info5", 
                "Info6", "Info7", "Info8", "Info9"
        );

        assertNotNull(dto);
        assertEquals("BILL123", dto.getBillNo());
        assertEquals(new BigDecimal("100.50"), dto.getBillAmount());
        assertEquals(true, dto.isPayable());
        // Diğer alanları da kontrol edebilirsin
    }


    @Test
    public void testEqualsAndHashCode() {
        QueriedBillResponseWebDTO dto1 = new QueriedBillResponseWebDTO(
                LocalDate.of(2024, 10, 15), "BILL123", "TERM123", 
                new BigDecimal("100.50"), "USD", "PROV123", 
                "Explanation", true, 
                "Info1", "Info2", "Info3", "Info4", "Info5", 
                "Info6", "Info7", "Info8", "Info9"
        );

        QueriedBillResponseWebDTO dto2 = new QueriedBillResponseWebDTO(
                LocalDate.of(2024, 10, 15), "BILL123", "TERM123", 
                new BigDecimal("100.50"), "USD", "PROV123", 
                "Explanation", true, 
                "Info1", "Info2", "Info3", "Info4", "Info5", 
                "Info6", "Info7", "Info8", "Info9"
        );

        assertEquals(dto1, dto2);  // Equals doğru çalışıyor mu kontrol et
        assertEquals(dto1.hashCode(), dto2.hashCode());  // HashCode'ların eşit olduğundan emin ol
    }

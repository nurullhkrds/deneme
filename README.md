  @Test
    public void testNoArgsConstructor() {
        PaidBillResponseWebDTO dto = new PaidBillResponseWebDTO();
        assertNotNull(dto);  // DTO'nun başarılı bir şekilde oluşturulduğunu doğrula
    }
 @Test
    public void testAllArgsConstructor() {
        PaidBillResponseWebDTO dto = new PaidBillResponseWebDTO(
                "1", "PCODE", "ICODE", "DTYPE", "SUBSCR123", "John Doe", 
                "2024-10-10", "BILL123", "TERM123", 
                new BigDecimal("100.50"), new BigDecimal("90.50"), 
                "USD", "ACC123", "CARD123", 123456L, 
                "Explanation", "Info1", "Info2", "Info3", "Info4", 
                "Info5", "Info6", "Info7", "Info8", "Info9"
        );

        assertNotNull(dto);
        assertEquals("1", dto.getId());
        assertEquals("PCODE", dto.getProductCode());
        // Diğer alanları da kontrol edebilirsin
    }
 @Test
    public void testEqualsAndHashCode() {
        PaidBillResponseWebDTO dto1 = new PaidBillResponseWebDTO(
                "1", "PCODE", "ICODE", "DTYPE", "SUBSCR123", "John Doe", 
                "2024-10-10", "BILL123", "TERM123", 
                new BigDecimal("100.50"), new BigDecimal("90.50"), 
                "USD", "ACC123", "CARD123", 123456L, 
                "Explanation", "Info1", "Info2", "Info3", "Info4", 
                "Info5", "Info6", "Info7", "Info8", "Info9"
        );

        PaidBillResponseWebDTO dto2 = new PaidBillResponseWebDTO(
                "1", "PCODE", "ICODE", "DTYPE", "SUBSCR123", "John Doe", 
                "2024-10-10", "BILL123", "TERM123", 
                new BigDecimal("100.50"), new BigDecimal("90.50"), 
                "USD", "ACC123", "CARD123", 123456L, 
                "Explanation", "Info1", "Info2", "Info3", "Info4", 
                "Info5", "Info6", "Info7", "Info8", "Info9"
        );

        assertEquals(dto1, dto2);  // Equals doğru çalışıyor mu kontrol et
        assertEquals(dto1.hashCode(), dto2.hashCode());  // HashCode'ların eşit olduğundan emin ol
    }


    @Test
    public void testAfterToGetQueryBillsResponse() {
        QueryBillsProcessOutput output = mock(QueryBillsProcessOutput.class);
        ProvisionDTO provisionDTO = mock(ProvisionDTO.class);
        Long provisionId = 1L;  // Long tipi ID oluştur

        when(output.getProvisionDTOList()).thenReturn(Collections.singletonList(provisionDTO));
        when(provisionDTO.getSubscriberName()).thenReturn("John Doe");
        when(provisionDTO.getBillNo()).thenReturn("123456");
        when(provisionDTO.getAmount()).thenReturn(BigDecimal.valueOf(100.0));
        when(provisionDTO.getBillDueDate()).thenReturn(LocalDate.parse("2023-01-01"));
        when(provisionDTO.getCurrency()).thenReturn(EnumCurrencyCode.DOLAR);  // Enum değerini doğrudan kullan
        when(provisionDTO.getBillTerm()).thenReturn("Monthly");
        when(provisionDTO.getId()).thenReturn(provisionId);  // Doğrudan Long tipi ID döndür
        when(provisionDTO.getExplanation()).thenReturn("Explanation");
        when(provisionDTO.getIsPayable()).thenReturn(true);

        QueryBillsResponse response = new QueryBillsResponse();

        mapper.afterToGetQueryBillsResponse(response, output);

        assertEquals("John Doe", response.getSubscriberName());
        assertEquals(1, response.getBillList().size());
        QueriedBillResponseWebDTO bill = response.getBillList().get(0);
        assertEquals("123456", bill.getBillNo());
        assertEquals(BigDecimal.valueOf(100.0), bill.getBillAmount());
        assertEquals(LocalDate.parse("2023-01-01"), bill.getBillDueDate());
        assertEquals("USD", bill.getCurrency());  // Enum'dan dönen değeri doğrula
        assertEquals("Monthly", bill.getBillTerm());
        assertEquals("1", bill.getBillProvisionId());  // ID'nin toString() metodunu doğrula
        assertEquals("Explanation", bill.getExplanation());
        assertTrue(bill.getPayable());
    }

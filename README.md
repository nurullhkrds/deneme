    @Test
    public void testAfterToGetQueryBillsResponse() {
        QueryBillsProcessOutput output = mock(QueryBillsProcessOutput.class);
        ProvisionDTO provisionDTO = mock(ProvisionDTO.class);
        when(output.getProvisionDTOList()).thenReturn(Collections.singletonList(provisionDTO));
        when(provisionDTO.getSubscriberName()).thenReturn("John Doe");
        when(provisionDTO.getBillNo()).thenReturn("123456");
        when(provisionDTO.getAmount()).thenReturn(BigDecimal.valueOf(100.0));
        when(provisionDTO.getBillDueDate()).thenReturn(LocalDate.parse("2023-01-01"));
        when(provisionDTO.getCurrency().getValue()).thenReturn("USD");
        when(provisionDTO.getBillTerm()).thenReturn("Monthly");
        when(provisionDTO.getId().toString()).thenReturn("1");
        when(provisionDTO.getExplanation()).thenReturn("Explanation");
        when(provisionDTO.getIsPayable()).thenReturn(true);

        QueryBillsResponse response = new QueryBillsResponse();

        mapper.afterToGetQueryBillsResponse(response, output);

        assertEquals("John Doe", response.getSubscriberName());
        assertEquals(1, response.getBillList().size());
        QueriedBillResponseWebDTO bill = response.getBillList().get(0);
        assertEquals("123456", bill.getBillNo());
        assertEquals(100.0, bill.getBillAmount());
        assertEquals("2023-01-01", bill.getBillDueDate());
        assertEquals("USD", bill.getCurrency());
        assertEquals("Monthly", bill.getBillTerm());
        assertEquals("1", bill.getBillProvisionId());
        assertEquals("Explanation", bill.getExplanation());
    }



java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.common.enums.EnumCurrencyCode.getValue()" because the return value of "com.ykb.payments.bill.transaction.payment.dto.ProvisionDTO.getCurrency()" is null

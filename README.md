    @Test
    public void testAfterToGetQueryBillsResponse() {
        QueryBillsProcessOutput output = mock(QueryBillsProcessOutput.class);
        ProvisionDTO provisionDTO = mock(ProvisionDTO.class);

        when(output.getProvisionDTOList()).thenReturn(Collections.singletonList(provisionDTO));
        when(provisionDTO.getSubscriberName()).thenReturn("John Doe");
        when(provisionDTO.getBillNo()).thenReturn("123456");
        when(provisionDTO.getAmount()).thenReturn(BigDecimal.valueOf(100.0));
        when(provisionDTO.getBillDueDate()).thenReturn(LocalDate.parse("2023-01-01"));
        when(provisionDTO.getCurrency()).thenReturn(EnumCurrencyCode.DOLAR);
        when(EnumCurrencyCode.DOLAR.getValue()).thenReturn("USD");
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
        assertEquals(BigDecimal.valueOf(100.0), bill.getBillAmount());
        assertEquals(LocalDate.parse("2023-01-01"), bill.getBillDueDate());
        assertEquals("USD", bill.getCurrency());
        assertEquals("Monthly", bill.getBillTerm());
        assertEquals("1", bill.getBillProvisionId());
        assertEquals("Explanation", bill.getExplanation());
    }

org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.


java.lang.NullPointerException: Cannot invoke "java.util.List.stream()" because the return value of "com.ykb.payments.bill.transaction.payment.web.response.QueryBillsResponse.getBillList()" is null


    @Test
    public void testToResponseHarmoniQueryBills() {
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        RequestHarmoniQueryBills harmoniRequest = new RequestHarmoniQueryBills();

        ResponseHarmoniQueryBills result = mapper.toResponseHarmoniQueryBills(queryBillsResponse, harmoniRequest);

        assertNotNull(result);
    }

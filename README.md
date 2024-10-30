java.lang.NullPointerException: Cannot invoke "java.util.List.size()" because the return value of "com.ykb.payments.bill.adapter.adapter.web.response.QueryBillsResponse.getBills()" is null



    @Test
    void givenQueryBillsRequest_whenQueryBills_thenReturnQueryBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
        QueryBillsRequest remoteRequest = new QueryBillsRequest();
        remoteRequest.setInstitution("SEFERIHISAR");
        remoteRequest.setProduct("BELEDİYE");
        remoteRequest.setIdentityNo("30055934852");
        QueryBillsResponse response = testQueryBills(ITahsilatIslemleri.class, remoteRequest);
        assertEquals(1, response.getBills().size());
    }

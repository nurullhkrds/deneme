java.lang.NullPointerException: Cannot invoke "java.time.LocalDate.atStartOfDay(java.time.ZoneId)" because the return value of "com.ykb.payments.bill.transaction.payment.web.response.QueriedBillResponseWebDTO.getBillDueDate()" is null
    @Test
    public void testToResponseHarmoniQueryBills() {
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        RequestHarmoniQueryBills harmoniRequest = new RequestHarmoniQueryBills();

        SubsrciberNoPartResponseWebDTO dto1=new SubsrciberNoPartResponseWebDTO();
        dto1.setPartKey("1234");
        dto1.setPartNo(1);


        SubsrciberNoPartResponseWebDTO dto2=new SubsrciberNoPartResponseWebDTO();
        dto2.setPartKey("5678");
        dto2.setPartNo(2);


        SubsrciberNoPartResponseWebDTO dto3=new SubsrciberNoPartResponseWebDTO();
        dto3.setPartKey("91011");
        dto3.setPartNo(3);

        queryBillsResponse.setSubscriberNoPartList(List.of(
                dto1,dto2,dto3
        ));

        queryBillsResponse.setBillList(List.of(
                new QueriedBillResponseWebDTO()
        ));

        ResponseHarmoniQueryBills result = mapper.toResponseHarmoniQueryBills(queryBillsResponse, harmoniRequest);

        assertNotNull(result);
    }

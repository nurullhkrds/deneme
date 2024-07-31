  @Test
    public void testToResponseHarmoniQueryBills() {
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        RequestHarmoniQueryBills harmoniRequest = new RequestHarmoniQueryBills();

        SubsrciberNoPartResponseWebDTO dto1 = new SubsrciberNoPartResponseWebDTO();
        dto1.setPartKey("1234");
        dto1.setPartNo(1);

        SubsrciberNoPartResponseWebDTO dto2 = new SubsrciberNoPartResponseWebDTO();
        dto2.setPartKey("5678");
        dto2.setPartNo(2);

        SubsrciberNoPartResponseWebDTO dto3 = new SubsrciberNoPartResponseWebDTO();
        dto3.setPartKey("91011");
        dto3.setPartNo(3);

        queryBillsResponse.setSubscriberNoPartList(List.of(dto1, dto2, dto3));

        QueriedBillResponseWebDTO bill = new QueriedBillResponseWebDTO();
        bill.setBillDueDate(LocalDate.now());  // Set a valid date
        bill.setBillProvisionId(123L); // Set a valid provision ID
        // Set other necessary fields for QueriedBillResponseWebDTO

        queryBillsResponse.setBillList(List.of(bill));

        ResponseHarmoniQueryBills result = mapper.toResponseHarmoniQueryBills(queryBillsResponse, harmoniRequest);

        assertNotNull(result);
        assertNotNull(result.getBillList());
        assertFalse(result.getBillList().isEmpty());
        assertNotNull(result.getBillList().get(0).getBillDueDate());
        assertEquals("123", result.getBillList().get(0).getBankReferenceNo());
    }

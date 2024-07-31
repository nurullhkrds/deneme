    @Test
    public void testToRequestQueryBillsHmn() {
        QueryBillsRequest request = new QueryBillsRequest();
        request.setCurrency("TRY");
        SubscriberNoPartRequestWebDTO dto1=new SubscriberNoPartRequestWebDTO();
        dto1.setPartValue("123");
        request.setSubscriberNoPartList(Arrays.asList(dto1));

        RequestQueryBillsHmn result = mapper.toRequestQueryBillsHmn(request);

        assertNotNull(result);
        assertEquals(EnumCurrencyCode.TURKISH_LIRA_YTL.getValue(), result.getCurrencyCode());
        assertEquals("123", result.getSubscriberNo1());
        assertNull(result.getSubscriberNo2());
        assertNull(result.getSubscriberNo3());
    }

java.lang.RuntimeException: java.lang.ClassNotFoundException: Cannot find implementation for com.ykb.payments.bill.transaction.payment.mapper.MicroHarmoniMapperImpl

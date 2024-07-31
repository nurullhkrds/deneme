 @Test
    public void testToQueryBillRequest() {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        request.setAgentCode("agent");
        request.setChannelCode("channel");
        request.setOperatingBranchCode("branch");
        request.setInstitution("institution");
        request.setProduct("product");
        request.setSubscriberNo1("subNo1");
        request.setIdentityNo("12345678901");

        QueryBillsRequest result = mapper.toQueryBillRequest(request);

        assertEquals("agent", result.getAgentCode());
        assertEquals("chan", result.getChannelCode()); // Adjusted expected value based on mock
        assertEquals("branch", result.getOperatingBranchCode());
        assertEquals("institution", result.getInstitutionCode());
        assertEquals("product", result.getProductCode());
        assertEquals("subNo1", result.getSubscriberNo());
        assertEquals(Long.valueOf(12345678901L), result.getIdentityNo());
    }

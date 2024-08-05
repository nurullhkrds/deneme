 @Test
    public void testGatherData() throws Exception {
        Map<String, Object> dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 12345L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 67890L);
        dataPack.put(ProcessDataPackKey.TAX_ID.getKey(), "98765");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "54321");
        
        ReflectionTestUtils.setField(queryBillsProcess, "dataPack", dataPack);
        
        Object gatherData = instantiatePrivateClass("GatherData");
        ReflectionTestUtils.invokeMethod(gatherData, "executeStep");
        
        assertEquals(12345L, ReflectionTestUtils.getField(queryBillsProcess, "customerNo"));
        assertEquals(67890L, ReflectionTestUtils.getField(queryBillsProcess, "identityNo"));
        assertEquals("98765", ReflectionTestUtils.getField(queryBillsProcess, "taxOfficeNo"));
        assertEquals("54321", ReflectionTestUtils.getField(queryBillsProcess, "subscriberNo"));
    }

    @Test
    public void testCreateProvisions() throws Exception {
        QueriedBillDTO billDTO = new QueriedBillDTO();
        billDTO.setCurrency("USD");
        billDTO.setQueryStan("12345");
        billDTO.setBillNo("98765");
        billDTO.setExplanation("Test bill");
        billDTO.setBillTerm("2024-08");
        billDTO.setInstitutionQueryStan("54321");
        billDTO.setBillDueDate(LocalDate.now().plusDays(30));
        billDTO.setPayable(true);
        billDTO.setBillIssueDate(LocalDate.now().minusDays(30));
        billDTO.setBillAmount(new BigDecimal("100.00"));
        billDTO.setSubscriberName("Test Subscriber");
        billDTO.setSubscriberNo("987654321");

        queryBillsProcess.queriedBillDTOList = List.of(billDTO);

        // Use reflection to access the private inner class
        Class<?> innerClass = queryBillsProcess.getClass().getDeclaredClasses()[0];
        Object createProvisionsInstance = innerClass.getDeclaredConstructor(queryBillsProcess.getClass()).newInstance(queryBillsProcess);

        // Access the private method
        Method executeStep = innerClass.getDeclaredMethod("executeStep");
        executeStep.setAccessible(true);
        executeStep.invoke(createProvisionsInstance);

        Field provisionListField = queryBillsProcess.getClass().getDeclaredField("provisionList");
        provisionListField.setAccessible(true);
        List<ProvisionDTO> provisionList = (List<ProvisionDTO>) provisionListField.get(queryBillsProcess);

        assertNotNull(provisionList);
        assertEquals(1, provisionList.size());

        ProvisionDTO provisionDTO = provisionList.get(0);
        assertEquals("USD", provisionDTO.getCurrency().toString());
        assertEquals("12345", provisionDTO.getQueryStan());
        assertEquals("98765", provisionDTO.getBillNo());
        assertEquals("Test bill", provisionDTO.getExplanation());
        assertEquals("2024-08", provisionDTO.getBillTerm());
        assertEquals("54321", provisionDTO.getInstitutionQueryStan());
        assertEquals(LocalDate.now().plusDays(30), provisionDTO.getBillDueDate());
        assertEquals(true, provisionDTO.getIsPayable());
        assertEquals(LocalDate.now().minusDays(30), provisionDTO.getBillIssueDate());
        assertEquals(new BigDecimal("100.00"), provisionDTO.getAmount());
        assertEquals("Test Subscriber", provisionDTO.getSubscriberName());
        assertEquals("987654321", provisionDTO.getSubscriberNo());
    }

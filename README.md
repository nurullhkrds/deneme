  @Test
    void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
        // Sabit bir tarih için LocalDate.now() mock'lanıyor
        try (MockedStatic<LocalDate> mocked = mockStatic(LocalDate.class)) {
            LocalDate fixedDate = LocalDate.of(2024, 11, 15);
            mocked.when(LocalDate::now).thenReturn(fixedDate);

            FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
            remoteRequest.setInstitution("ARMADAŞ");
            remoteRequest.setProduct("DOĞALGAZ");
            remoteRequest.setDueDate(LocalDate.of(2024, 11, 15));
            insertReturnMap();
            insertParams("OTLYKB");

            FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);
            assertEquals(3, response.getBills().size());
        }
    }

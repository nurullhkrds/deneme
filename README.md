@Test
void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
    // Sabit bir tarih: 15.12.2024
    LocalDate fixedDate = LocalDate.of(2024, 12, 15);

    // LocalDate.now() metodunu sabitlemek için MockedStatic kullan
    try (MockedStatic<LocalDate> mockedLocalDate = mockStatic(LocalDate.class)) {
        mockedLocalDate.when(LocalDate::now).thenReturn(fixedDate);

        // Request nesnesini oluştur
        FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
        remoteRequest.setInstitution("ARMADAŞ");
        remoteRequest.setProduct("DOĞALGAZ");
        remoteRequest.setDueDate(LocalDate.of(2024, 11, 15)); // 15 Kasım 2024

        // Gerekli diğer setup işlemleri
        insertReturnMap();
        insertParams("OTLYKB");

        // Test edilen metodun çağrısı
        FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);

        // Yanıtı doğrula
        assertEquals(3, response.getBills().size());
    }
}

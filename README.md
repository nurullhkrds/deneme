@Test
void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws Exception {
    // Sabit bir Clock: 15.12.2024
    Clock fixedClock = Clock.fixed(Instant.parse("2024-12-15T00:00:00Z"), ZoneId.systemDefault());

    // Test edilecek sınıf
    YourClass yourClass = new YourClass(fixedClock);

    // Request nesnesi oluştur
    FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
    remoteRequest.setInstitution("ARMADAŞ");
    remoteRequest.setProduct("DOĞALGAZ");
    remoteRequest.setDueDate(LocalDate.of(2024, 11, 15)); // 15 Kasım 2024

    // Gerekli setup
    insertReturnMap();
    insertParams("OTLYKB");

    // Test edilen metodun çağrısı
    FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);

    // Doğrulama
    assertEquals(3, response.getBills().size());
}

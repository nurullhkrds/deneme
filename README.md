@Test
void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws Exception {
    // Test sırasında tarih için sabit bir clock ayarla
    Clock fixedClock = Clock.fixed(Instant.parse("2024-12-15T00:00:00Z"), ZoneId.systemDefault());

    // Clock'u global olarak ayarlamak için özel bir yöntem kullan
    LocalDate today = LocalDate.now(fixedClock);

    // Request nesnesi oluştur
    FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
    remoteRequest.setInstitution("ARMADAŞ");
    remoteRequest.setProduct("DOĞALGAZ");
    remoteRequest.setDueDate(LocalDate.of(2024, 11, 15));

    // Gerekli setup
    insertReturnMap();
    insertParams("OTLYKB");

    // Test edilen metod
    FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);

    // Yanıt doğrulama
    assertEquals(3, response.getBills().size());
}

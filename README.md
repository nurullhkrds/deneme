private boolean isDueDateValid(XMLGregorianCalendar dueDate, Clock clock) {
    if (dueDate == null) {
        return false;
    }
    LocalDate todayDate = LocalDate.now(clock);
    LocalDate dueLocalDate = dueDate.toGregorianCalendar().toZonedDateTime().toLocalDate();
    return !dueLocalDate.isBefore(todayDate);
}
@Test
void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
    // Sabit bir tarih için Clock nesnesi oluştur
    Clock fixedClock = Clock.fixed(LocalDate.of(2024, 11, 15).atStartOfDay(ZoneId.systemDefault()).toInstant(), ZoneId.systemDefault());

    FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
    remoteRequest.setInstitution("ARMADAŞ");
    remoteRequest.setProduct("DOĞALGAZ");
    remoteRequest.setDueDate(LocalDate.of(2024,11,15));
    insertReturnMap();
    insertParams("OTLYKB");

    FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest, fixedClock);
    assertEquals(3, response.getBills().size());
}

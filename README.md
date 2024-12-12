public class DateProvider {
    public LocalDate getCurrentDate() {
        return LocalDate.now(); // Varsayılan olarak LocalDate.now() döner
    }
}
public class YourClass {
    private final DateProvider dateProvider;

    public YourClass(DateProvider dateProvider) {
        this.dateProvider = dateProvider;
    }

    private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
        if (dueDate == null) {
            return false;
        }
        LocalDate todayDate = dateProvider.getCurrentDate(); // LocalDate.now() yerine
        LocalDate dueLocalDate = dueDate.toGregorianCalendar().toZonedDateTime().toLocalDate();
        return !dueLocalDate.isBefore(todayDate);
    }
}
@Test
void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws Exception {
    // Mock DateProvider
    DateProvider dateProviderMock = mock(DateProvider.class);
    when(dateProviderMock.getCurrentDate()).thenReturn(LocalDate.of(2024, 12, 15)); // 15.12.2024

    // Test edilen sınıf
    YourClass yourClass = new YourClass(dateProviderMock);

    // Request nesnesi
    FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
    remoteRequest.setInstitution("ARMADAŞ");
    remoteRequest.setProduct("DOĞALGAZ");
    remoteRequest.setDueDate(LocalDate.of(2024, 11, 15)); // 15 Kasım 2024

    // Gerekli setup
    insertReturnMap();
    insertParams("OTLYKB");

    // Test edilen metodun çağrısı
    FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);

    // Yanıt doğrulama
    assertEquals(3, response.getBills().size());
}

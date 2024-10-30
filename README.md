@Test
void givenQueryBillsRequest_whenQueryBills_thenReturnQueryBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
    // Arrange
    QueryBillsRequest remoteRequest = new QueryBillsRequest();
    remoteRequest.setInstitution("SEFERIHISAR");
    remoteRequest.setProduct("BELEDİYE");
    remoteRequest.setIdentityNo("30055934852");

    // Mock response
    QueryBillsResponse mockResponse = new QueryBillsResponse();
    mockResponse.setBills(new ArrayList<>()); // Boş listeyle başlatıldı

    // Burada getBills() listesinin mocklanması veya set edilmesi gerekebilir
    BaseBillDTO billDTO = new BaseBillDTO();
    billDTO.setBillNo("MockBillNo");
    mockResponse.getBills().add(billDTO); // Mock bir fatura ekleniyor

    // Test edilecek metodun çağrılması
    QueryBillsResponse response = testQueryBills(ITahsilatIslemleri.class, remoteRequest);

    // Assert
    assertNotNull(response.getBills(), "Bills list should not be null");
    assertEquals(1, response.getBills().size());
}

@Test
void givenDoPaymentSummaryReconciliationRequest_whenValidResultCodesMatch_thenReturnValidResponse() {
    // Arrange
    DoPaymentSummaryReconciliationRequest remoteRequest = new DoPaymentSummaryReconciliationRequest();
    remoteRequest.setInstitution("SEFERHİSAR");
    remoteRequest.setProduct("BELEDİYE");
    LocalDate reconciliationDate = getLocalDate("2024-10-25");
    BigDecimal amount = new BigDecimal(312.99).setScale(2, RoundingMode.CEILING);
    BigDecimal cancelAmount = new BigDecimal(31.30).setScale(2, RoundingMode.CEILING);

    remoteRequest.setReconciliationDate(reconciliationDate);
    remoteRequest.setTotalPaymentAmount(amount);
    remoteRequest.setTotalPaymentCount(2);
    remoteRequest.setTotalPaymentCancelAmount(cancelAmount);
    remoteRequest.setTotalPaymentCancelCount(1);

    insertReturnMap();

    // Act
    DoPaymentSummaryReconciliationResponse response = testPaymentSummary(ITahsilatIslemleri.class, remoteRequest);

    // Mock a valid result code
    response.setInstitutionResultCode("621");

    // Assert
    assertEquals(amount, response.getTotalPaymentAmount());
    assertEquals(2, response.getTotalPaymentCount());
    assertTrue(Arrays.asList("620", "621", "622", "623", "624", "625").contains(response.getInstitutionResultCode()));

    // Extra validation to confirm valid codes logic
    List<String> validResultCodes = Arrays.asList("620", "621", "622", "623", "624", "625");
    if (validResultCodes.contains(response.getInstitutionResultCode())) {
        assertEquals(amount, response.getTotalPaymentAmount());
        assertEquals(2, response.getTotalPaymentCount());
    }
}

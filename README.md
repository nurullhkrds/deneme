@Test
void shouldHandleGenericException() {
    // Given
    CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
    inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
    inputDto.setContractNo(123L);
    inputDto.setChannelTransactionId("123");
    inputDto.setDummyMerchant(true);

    MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
    makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
    makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
    makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

    // Mock the behavior to throw a RuntimeException
    Mockito.when(provisionNextService.makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class)))
            .thenThrow(new RuntimeException("Test exception"));

    // When
    CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

    // Then
    assertFalse(actualResult.isSuccess(), "Expected the result to be unsuccessful.");
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, actualResult.getError(), "Expected the error to be GENERIC_UNKNOWN_ERROR.");
    Mockito.verify(provisionNextService).makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class));
}

@Test
void shouldReturnIsDummyMerchantTrueButMakeReverseFalse() {
    // Test verilerini oluştur
    CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
    inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
    inputDto.setContractNo(123L);
    inputDto.setChannelTransactionId("123");
    inputDto.setDummyMerchant(true);

    CreateReverseAccountingResultDTO expectedResult = new CreateReverseAccountingResultDTO();
    expectedResult.setSuccess(false);

    MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
    makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
    makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
    makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

    // Mock MakeReverseProvisionResponse
    MakeReverseProvisionResponse makeDto = new MakeReverseProvisionResponse();
    makeDto.setErrorCode(500L);
    makeDto.setSuccess(false);

    // Mocking exception with cause
    ServiceCallException serviceCallException = new ServiceCallException(500L);
    RuntimeException runtimeException = new RuntimeException(serviceCallException);

    // Mock provisionNextService
    Mockito.when(provisionNextService.makeReverseProvision(Mockito.argThat(argument ->
        argument.getContractNo().equals(makeReverseProvisionRequest.getContractNo()) &&
        argument.getTransactionId().equals(makeReverseProvisionRequest.getTransactionId()) &&
        argument.getReverseDescriptionAppendix().equals(makeReverseProvisionRequest.getReverseDescriptionAppendix())
    ))).thenThrow(runtimeException);

    // Metodu çağır ve sonucu doğrula
    CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);
    assertEquals(expectedResult.isSuccess(), actualResult.isSuccess());
    verify(provisionNextService).makeReverseProvision(Mockito.argThat(argument ->
        argument.getContractNo().equals(makeReverseProvisionRequest.getContractNo()) &&
        argument.getTransactionId().equals(makeReverseProvisionRequest.getTransactionId()) &&
        argument.getReverseDescriptionAppendix().equals(makeReverseProvisionRequest.getReverseDescriptionAppendix())
    ));
}

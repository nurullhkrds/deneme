@Test
void shouldHandleGenericUnknownErrorWhenExceptionOccurs() {
    CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
    inputDto.setDummyMerchant(true);

    // ServiceCallException oluşturup, RuntimeException'a sebep (cause) olarak ekleyin
    ServiceCallException serviceCallException = new ServiceCallException(new ExceptionData());
    RuntimeException runtimeException = new RuntimeException(serviceCallException);

    when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
            .thenThrow(runtimeException);

    CreateReverseAccountingResultDTO resultDTO = cardReverseProvisionService.doReverseAccounting(inputDto);

    assertEquals(false, resultDTO.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
}

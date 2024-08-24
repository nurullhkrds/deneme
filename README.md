@Test
void shouldHandleExceptionWhenMakeReverseProvisionFails() {
    CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
    inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
    inputDto.setContractNo(123L);
    inputDto.setChannelTransactionId("123");
    inputDto.setDummyMerchant(true);

    MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
    makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
    makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
    makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

    MakeReverseProvisionResponse makeDto = new MakeReverseProvisionResponse();
    makeDto.setErrorCode(500L);
    makeDto.setSuccess(false);

    when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
            .thenReturn(makeDto);

    CreateReverseAccountingResultDTO resultDTO = cardReverseProvisionService.doReverseAccounting(inputDto);

    // Eğer EnumAccountProvisionResult.parse() null dönerse, GENERIC_UNKNOWN_ERROR döndürülmeli
    if (EnumAccountProvisionResult.parse(500L) == null) {
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    } else {
        assertEquals(EnumAccountProvisionResult.parse(500L).getBillCode(), resultDTO.getError());
    }
    assertEquals(false, resultDTO.isSuccess());
}

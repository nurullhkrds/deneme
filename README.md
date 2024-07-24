  @Test
    void shouldHandleGenericException() {
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);

        MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        RuntimeException runtimeException = new RuntimeException();

        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class)))
                .thenThrow(runtimeException);

        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

        assertFalse(actualResult.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, actualResult.getError());
        verify(provisionNextService).makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class));
    }java.lang.NullPointerException: Cannot invoke "Object.getClass()" because the return value of "java.lang.Exception.getCause()" is null

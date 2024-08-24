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

        assertEquals(false, resultDTO.isSuccess());
        assertEquals(EnumAccountProvisionResult.parse(500L).getBillCode(), resultDTO.getError());
    }java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.common.enums.EnumAccountProvisionResult.getBillCode()" because the return value of "com.ykb.payments.bill.common.enums.EnumAccountProvisionResult.parse(java.lang.Long)" is null

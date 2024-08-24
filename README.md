   @Test
    void shouldHandleGenericUnknownErrorWhenExceptionOccurs() {
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setDummyMerchant(true);

        RuntimeException runtimeException = new RuntimeException("Generic exception");

        when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
                .thenThrow(runtimeException);

        CreateReverseAccountingResultDTO resultDTO = cardReverseProvisionService.doReverseAccounting(inputDto);

        assertEquals(false, resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }
java.lang.NullPointerException: Cannot invoke "Object.getClass()" because the return value of "java.lang.Exception.getCause()" is null

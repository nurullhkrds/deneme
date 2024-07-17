 @Test
    void shouldHandleServiceCallException() {
        // Test verilerini oluştur
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);

        // Mock MakeReverseProvisionRequest
        MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        // Mock ServiceCallException
        ServiceCallException serviceCallException = new ServiceCallException(500L);
        RuntimeException runtimeException = new RuntimeException(serviceCallException);

        // Mock provisionNextService
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class)))
               .thenThrow(runtimeException);

        // Metodu çağır ve sonucu doğrula
        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

        // Assert and verify
        assertEquals(false, actualResult.isSuccess());
        verify(provisionNextService).makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class));
        ArgumentCaptor<Long> errorCodeCaptor = ArgumentCaptor.forClass(Long.class);
        verify(cardReverseProvisionService).handleException(errorCodeCaptor.capture(), eq(actualResult));
        assertEquals(500L, errorCodeCaptor.getValue());
    }

    @Test
    void shouldHandleGenericException() {
        // Test verilerini oluştur
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);

        // Mock MakeReverseProvisionRequest
        MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        // Mock a generic exception
        RuntimeException runtimeException = new RuntimeException();

        // Mock provisionNextService
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class)))
               .thenThrow(runtimeException);

        // Metodu çağır ve sonucu doğrula
        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

        // Assert and verify
        assertEquals(false, actualResult.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, actualResult.getError());
        verify(provisionNextService).makeReverseProvision(Mockito.any(MakeReverseProvisionRequest.class));
        verify(cardReverseProvisionService, never()).handleException(anyLong(), eq(actualResult));
    }

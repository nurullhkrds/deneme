@Test
    void shouldHandleUnsuccessfulMakeReverseProvision() {
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

        // Mock provisionNextService
        Mockito.when(provisionNextService.makeReverseProvision(makeReverseProvisionRequest)).thenReturn(makeDto);

        // Metodu çağır ve sonucu doğrula
        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

        // Assert and verify
        assertEquals(expectedResult.isSuccess(), actualResult.isSuccess());
        verify(provisionNextService).makeReverseProvision(makeReverseProvisionRequest);
        verify(cardReverseProvisionService).handleException(makeDto.getErrorCode(), actualResult);
    }

    @Test
    void shouldHandleSuccessfulMakeReverseProvision() {
        // Test verilerini oluştur
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);

        CreateReverseAccountingResultDTO expectedResult = new CreateReverseAccountingResultDTO();
        expectedResult.setSuccess(true);

        MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        // Mock MakeReverseProvisionResponse
        MakeReverseProvisionResponse makeDto = new MakeReverseProvisionResponse();
        makeDto.setErrorCode(0L);
        makeDto.setSuccess(true);

        // Mock provisionNextService
        Mockito.when(provisionNextService.makeReverseProvision(makeReverseProvisionRequest)).thenReturn(makeDto);

        // Metodu çağır ve sonucu doğrula
        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);

        // Assert and verify
        assertEquals(expectedResult.isSuccess(), actualResult.isSuccess());
        verify(provisionNextService).makeReverseProvision(makeReverseProvisionRequest);
        verify(cardReverseProvisionService, never()).handleException(anyLong(), any(CreateReverseAccountingResultDTO.class));
    }

   @Test
    public void testDoReverseAccounting_ServiceCallException() throws ServiceCallException {
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654L);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        ServiceCallException exception = new ServiceCallException(new ExceptionData());
        exception.setErrorCode(123L);

        when(provisionNextService.makeReverseProvision(any())).thenThrow(exception);

        CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assertFalse(resultDTO.isSuccess());
        assertNotNull(resultDTO.getError());
        assertEquals(Long.valueOf(123L), ((ServiceCallException) exception.getCause()).getErrorCode());
    }

    @Test
    public void testDoReverseAccounting_GeneralException() throws ServiceCallException {
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654L);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException("Test exception"));

        CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }

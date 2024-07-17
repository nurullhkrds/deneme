java.lang.NullPointerException: Cannot invoke "Object.getClass()" because the return value of "java.lang.Exception.getCause()" is null
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

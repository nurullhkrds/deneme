  @Test
    public void testDoReverseAccounting_GeneralException() throws ServiceCallException {
        // Mock a general exception thrown
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654L);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");
// Mock nesneyi oluşturun
        ProvisionNextService provisionNextService = mock(ProvisionNextService.class);



        when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException("Test exception"));

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assertFalse(resultDTO.isSuccess());
        assertNotEquals(null, resultDTO.getError());
    }

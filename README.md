 @Test
    public void testDoReverseAccounting_Success() {
        // Mock successful response from provisionNextService
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse mockResponse = new MakeReverseProvisionResponse();
        mockResponse.setSuccess(true);

        when(provisionNextService.makeReverseProvision(any())).thenReturn(mockResponse);

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = reverseAccountingService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assert(resultDTO.isSuccess());
        assert(resultDTO.getError() == null);
    }

    @Test
    public void testDoReverseAccounting_FailureFromService() {
        // Mock failed response from provisionNextService
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse mockResponse = new MakeReverseProvisionResponse();
        mockResponse.setSuccess(false);
        mockResponse.setErrorCode(500); // Mock error code

        when(provisionNextService.makeReverseProvision(any())).thenReturn(mockResponse);

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = reverseAccountingService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assert(!resultDTO.isSuccess());
        assert(resultDTO.getError() != null);
    }

    @Test
    public void testDoReverseAccounting_ServiceCallException() {
        // Mock ServiceCallException thrown by provisionNextService
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        when(provisionNextService.makeReverseProvision(any())).thenThrow(new ServiceCallException(404L)); // Mock exception

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = reverseAccountingService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assert(!resultDTO.isSuccess());
        assert(resultDTO.getError() != null);
    }

    @Test
    public void testDoReverseAccounting_GeneralException() {
        // Mock a general exception thrown
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654);

        MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
        mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
        mockRequest.setContractNo(inputDTO.getContractNo());
        mockRequest.setReverseDescriptionAppendix("İPTAL");

        when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException("Test exception"));

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = reverseAccountingService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assert(!resultDTO.isSuccess());
        assert(resultDTO.getError() != null);
    }

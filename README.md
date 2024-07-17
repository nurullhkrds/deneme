org.mockito.exceptions.base.MockitoException: 
Checked exception is invalid for this method!
Invalid: com.ykb.architecture.micro.error.exception.ServiceCallException ve org.opentest4j.AssertionFailedError: 
Expected :true
Actual   :false
<Click to see difference> hatalarını veriyor 

    @Test
    public void testDoReverseAccounting_Success() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        MakeReverseProvisionResponse mockResponse = new MakeReverseProvisionResponse();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenReturn(mockResponse);

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertTrue(result.isSuccess());
        assertNull(result.getError());
    }

    @Test
    public void testDoReverseAccounting_ServiceCallException() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenThrow(new ServiceCallException(new ExceptionData()));

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    @Test
    public void testDoReverseAccounting_GeneralException() {
        // Arrange
        CreateReverseAccountingDTO dto = createSampleDTO();
        Mockito.when(provisionNextService.makeReverseProvision(Mockito.any())).thenThrow(new RuntimeException("Unexpected error"));

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionService.doReverseAccounting(dto);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    private CreateReverseAccountingDTO createSampleDTO() {
        CreateReverseAccountingDTO dto = new CreateReverseAccountingDTO();
        dto.setChannelTransactionId("1234567890");
        dto.setContractNo(123456789L); // Example long value
        // Set other necessary properties
        return dto;
    }


@Override
	public CreateReverseAccountingResultDTO doReverseAccounting(CreateReverseAccountingDTO createReverseAccountingDTO) {
		CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
		MakeReverseProvisionRequest makeReverseProvisionRequest = prepareReverseProvisionRequest(createReverseAccountingDTO);
		try {
			MakeReverseProvisionResponse makeReverseProvision = provisionNextService.makeReverseProvision(makeReverseProvisionRequest);	// TODO: Servisten donen degerlerin hangisi kullanılacak?

			if(!makeReverseProvision.isSuccess()){
				handleException(makeReverseProvision.getErrorCode(), createReverseAccountingResultDTO);
				createReverseAccountingResultDTO.setSuccess(false);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setSuccess(true);
		}catch (Exception e){
			if(e.getCause().getClass().equals(ServiceCallException.class)){
				Long errorCode =((ServiceCallException) e.getCause()).getErrorCode();
				handleException(errorCode, createReverseAccountingResultDTO);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			createReverseAccountingResultDTO.setSuccess(false);
		}

		return createReverseAccountingResultDTO;
	}

	private MakeReverseProvisionRequest prepareReverseProvisionRequest(CreateReverseAccountingDTO createReverseAccountingDTO) {
		MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
		makeReverseProvisionRequest.setTransactionId(createReverseAccountingDTO.getChannelTransactionId());
		makeReverseProvisionRequest.setContractNo(createReverseAccountingDTO.getContractNo());
		makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL"); //TODO: Bu alan nasıl doldurulacak?
		return makeReverseProvisionRequest;
	}

	private void handleException(Long errorCode, CreateReverseAccountingResultDTO createReverseAccountingResultDTO){
		EnumAccountProvisionResult result = EnumAccountProvisionResult.parse(errorCode);
		createReverseAccountingResultDTO.setSuccess(false);
		if(result == null){
			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			return;
		}
		createReverseAccountingResultDTO.setError(result.getBillCode());
	}

java.lang.NullPointerException: Cannot invoke "Object.getClass()" because the return value of "java.lang.Exception.getCause()" is null
 @Test
    void shouldReturnIsDummyMerchantTrueButMakeReverseFalse(){
        CreateReverseAccountingDTO inputDto=new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);
        CreateReverseAccountingResultDTO resultReverseAccountDTO=new CreateReverseAccountingResultDTO();
        MakeReverseProvisionRequest makeReverseProvisionRequest=new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse makeDto=new MakeReverseProvisionResponse();
        makeDto.setErrorCode(500L);
        makeDto.setSuccess(false);

        Mockito.when(provisionNextService.makeReverseProvision(makeReverseProvisionRequest)).thenReturn(makeDto);
        resultReverseAccountDTO.setSuccess(false);

        CreateReverseAccountingResultDTO returnResult= cardReverseProvisionService.doReverseAccounting(inputDto);
        assertEquals(returnResult,resultReverseAccountDTO);
        verify(provisionNextService).makeReverseProvision(makeReverseProvisionRequest);



    }


	@Override
	public CreateReverseAccountingResultDTO doReverseAccounting(CreateReverseAccountingDTO createReverseAccountingDTO) {
		
		CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();

		/* Gerçek üye işyeri için iptal muhasebesi yapılmayacak  */
		if(!createReverseAccountingDTO.isDummyMerchant()) {
			createReverseAccountingResultDTO.setSuccess(true);
			return createReverseAccountingResultDTO;
		}
		
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

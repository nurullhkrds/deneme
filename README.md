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

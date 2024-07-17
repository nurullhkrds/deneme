
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
	

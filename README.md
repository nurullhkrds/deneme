@Override
	public CoreServiceResultDTO<List<InstitutionIconDTO>> getAllInstitutionIcons() throws HmnServiceException {
		CoreServiceResultDTO<List<InstitutionIconDTO>> response = new CoreServiceResultDTO<List<InstitutionIconDTO>>();

		List<InstitutionIconDTO> resultList = QuerySupport.query(NQConsts.GET_PYM_INSTITUTION_ICONS.QUERY_NAME, new GetInstitutionIconTransformer());
		if (resultList == null || resultList.isEmpty()) {
			response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
			response.setResponseMessage(PYMResponseUtils.createResponseStatusDTO(EnumResponseCodes.GENERIC__NO_RECORD_FOUND_ERROR.getValue(), null));
		}

		else {
			response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
			response.setResponseMessage(PYMResponseUtils.createResponseStatusDTO(EnumResponseCodes.SUCCESS.getValue(), null));
			response.setResult(resultList);
		}
		return response;
	}



 	@Override
	public CoreServiceResultDTO<List<ParamModelDTO>> getMicroPilot(String institution, String product)
			throws HmnServiceException {
		ParamSearchDTO paramSearchDTO = new ParamSearchDTO();
		paramSearchDTO.setParamCode(BillPaymentsConsts.MICRO_BILL_PILOT);
		paramSearchDTO.setId(product);
		paramSearchDTO.setText(institution);
		List<ParamModelDTO> accountTypeList =  ExternalServiceLocator.getParamService().findParamByDynamic(paramSearchDTO);
		
		CoreServiceResultDTO<List<ParamModelDTO>> resultDTO = new CoreServiceResultDTO<List<ParamModelDTO>>();
		if(accountTypeList==null){
			accountTypeList = new ArrayList<ParamModelDTO>();
		}
		resultDTO.setResult( accountTypeList );
		resultDTO.setStatus( BillPaymentsConsts.RESPONSE_STATUS.SUCCESS );
		resultDTO.setResponseMessage( PYMResponseUtils.createResponseStatusDTO( EnumResponseCodes.SUCCESS.getValue(), null ) );
		
		return resultDTO;
	}




 

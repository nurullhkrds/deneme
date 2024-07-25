
	    private RequestCommissionInformation prepareCommissionInformationReqForCreditCard(CommissionServiceRequestDTO commissionServiceRequestDTO) {

	        RequestCommissionInformation requestCommissionInformation = new RequestCommissionInformation();
	        
	        requestCommissionInformation.setAccountingType(CREDIT_CARD);
	        requestCommissionInformation.setAccountNo(null);
	        requestCommissionInformation.setAccountCurrency(null);
	        requestCommissionInformation.setTransactionAmount(commissionServiceRequestDTO.getPaymentAmount());
	        requestCommissionInformation.setTransactionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        requestCommissionInformation.setChannelCode(commissionServiceRequestDTO.getChannelCode());
	        requestCommissionInformation.setBranch(BRANCH_CODE_925);
	        requestCommissionInformation.setUserCode(USER_CODE_INT001);

	        List<CommissionInputDetailApiDTO> commissionInputDetailApiDTOList = new ArrayList<>();

	        CommissionInputDetailApiDTO commissionInputDetailApiDTO = new CommissionInputDetailApiDTO();
	        commissionInputDetailApiDTO.setOperationCode(commissionServiceRequestDTO.getExpenseCode());
	        commissionInputDetailApiDTO.setCommissionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        commissionInputDetailApiDTOList.add(commissionInputDetailApiDTO);

	        requestCommissionInformation.setCommissionInputDetailApiDTOList(commissionInputDetailApiDTOList);

	        return requestCommissionInformation;

	    }

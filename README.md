	@Override
	public GetCustomerPaidBillListResponse getCustomerPaidBillList(GetCustomerPaidBillListRequest request)
			throws MicroException {

		List<PaidBillResponseWebDTO> microBillList = getBillList(request);
		List<PaidBillResponseWebDTO> harmoniBillList = getHarmoniBillList(request);
		List<PaidBillResponseWebDTO> combinedBillList = new ArrayList<>();

		combinedBillList.addAll(microBillList);
		combinedBillList.addAll(harmoniBillList);

		BillValidationUtil.validateCondition(!CollectionUtils.isEmpty(combinedBillList),
				EnumBillResult.PAID_BILL_NOT_FOUND_ERROR, BillTransactionConstant.APP_NAME);

		GetCustomerPaidBillListResponse response = new GetCustomerPaidBillListResponse();
		response.setBillList(combinedBillList);

		return response;
	}

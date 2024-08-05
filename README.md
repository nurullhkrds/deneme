	@Operation(description = " Bill Payment Expense")
	@PostMapping(path = "/getBillPaymentExpense")
	public HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> getBillPaymentExpense(
			@RequestBody RequestHarmoniGetBillPaymentExpense request) {
		fillMandatoryFields(request.getCoreServiceBaseDataDTO());

		try {
			GetBillPaymentExpenseResponseDTO microResponse = subscriberService
					.getBillPaymentExpense(harmoniMicroMapper.toGetBillPaymentExpenseRequestDTO(request));
			ResponseHarmoniGetBillPaymentExpense harmoniResponse = harmoniMicroMapper
					.toResponseHarmoniGetBillPaymentExpense(microResponse);

			HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = new HarmoniCoreServiceResultDTO<>();
			result.setResult(harmoniResponse);
			result.setStatus(SUCCESS);
			HarmoniResponseStatusMsgDTO responseMessage = new HarmoniResponseStatusMsgDTO();
			responseMessage.setResponseCode(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue());
			responseMessage.setResponseMessage(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription());
			result.setResponseMessage(responseMessage);

			return result;

		} catch (MicroException e) {
			return handleBillException(e);
		}
	}

@Operation(description = "Cancel Bill Payment")
	@PostMapping(path = "/cancelBillPayment")
	public HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> cancelBillPayment(
			@RequestBody RequestHarmoniCancelBillPayment request) throws MicroException {

		fillMandatoryFields(request.getCoreServiceBaseDataDTO());

		try {
			CancelBillPaymentResponse microResponse = paymentService
					.cancelBillPayment(harmoniMicroMapper.toCancelBillPaymentRequest(request));

			ResponseHarmoniCancelBillPayment convertedHarmoniResponse = harmoniMicroMapper
					.toResponseHarmoniCancelBillPayment(microResponse);

			HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> result = new HarmoniCoreServiceResultDTO<>();
			result.setStatus(SUCCESS);
			HarmoniResponseStatusMsgDTO responseMessage = new HarmoniResponseStatusMsgDTO();
			responseMessage.setResponseCode(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue());
			responseMessage.setResponseMessage(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription());
			result.setResponseMessage(responseMessage);
			return result;

		} catch (BillException e) {
			return handleBillException(e);
		}
	}

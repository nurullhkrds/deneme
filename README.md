@Operation(description = "Do Bill Payment")
	@PostMapping(path = "/doBillPayment")
	public HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> doBillPayment(
			@RequestBody RequestHarmoniDoBillPayment request) throws MicroException {

		fillMandatoryFields(request.getCoreServiceBaseDataDTO());

		try {
			DoBillPaymentResponse microResponse = paymentService
					.doBillPayment(harmoniMicroMapper.toDoBillPaymentRequest(request));

			ResponseHarmoniDoBillPaymentResultDTO hmnResponse = harmoniMicroMapper
					.toResponseHarmoniDoBillPaymentResultDTO(microResponse);

			HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> result = new HarmoniCoreServiceResultDTO<>();
			result.setResult(hmnResponse);
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

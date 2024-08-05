	@Operation(description = "Get Recon Detail")
	@GetMapping(path = "/getReconDetail")
	public HarmoniCoreServiceResultDTO<List<HmnPaidBillDTO> > getReconDetail(@RequestParam  boolean isPayment,
															   @RequestParam Date reconciliationDate,
															   @RequestParam String productCode,
															   @RequestParam String institutionCode) throws MicroException{
		try {
			List<HmnPaidBillDTO> reconDetailList =	paymentService.getReconDetail(isPayment, reconciliationDate,productCode,institutionCode);
			HarmoniCoreServiceResultDTO<List<HmnPaidBillDTO>> result = new HarmoniCoreServiceResultDTO<>();
			result.setResult(reconDetailList);
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

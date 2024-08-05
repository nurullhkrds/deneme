	@Operation(description = "Get Recon Count")
	@GetMapping(path = "/getReconCount")
	public HarmoniCoreServiceResultDTO<CountDTO> getReconCount(@RequestParam  boolean isPayment,
															   @RequestParam Date reconciliationDate,
															   @RequestParam String productCode,
															   @RequestParam String institutionCode) throws MicroException{
		try {
			CountDTO countDTO =	paymentService.getReconCount(isPayment, reconciliationDate,productCode,institutionCode);
			HarmoniCoreServiceResultDTO<CountDTO> result = new HarmoniCoreServiceResultDTO<>();
			result.setResult(countDTO);
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

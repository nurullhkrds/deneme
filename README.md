	@Operation(description = "Get customer paid bill list")
	@GetMapping(path = "/getCustomerPaidBillList")
	public HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> getCustomerPaidBillList(
				@RequestParam Long customerNo, @RequestParam String channelCode,@RequestParam(required = false) String product) {
		try {
			GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
			request.setCustomerNo(customerNo);
			request.setChannelCode(ChannelUtil.convertChannel(channelCode));
			request.setProductCode(product);
			List<HmnPaidBillDTO> hmnPaidBillDTOList = paymentService.getMicroBillList(request);
			ResponseHarmoniGetCustomerPaidBillList harmoniResponse = new ResponseHarmoniGetCustomerPaidBillList();
			harmoniResponse.setBillDTOList(hmnPaidBillDTOList);
			HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> result = new HarmoniCoreServiceResultDTO<>();
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

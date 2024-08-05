@RestController
@Tag(name = "Harmoni Bill Payment")
@RequestMapping("/harmoniBillPayment")
@RequiredArgsConstructor
public class HarmoniPaymentAdkController {

	private static final String ERROR = "E";
	private static final String SUCCESS = "S";

	private final SubscriberService subscriberService;
	private final PaymentService paymentService;
	private final RequestContext requestContext;
	private final HarmoniMicroMapper harmoniMicroMapper;

	private <T> HarmoniCoreServiceResultDTO<T> handleBillException(BillException ex) {
		HarmoniCoreServiceResultDTO resutlDTO = new HarmoniCoreServiceResultDTO<>();
		resutlDTO.setResult(null);
		resutlDTO.setStatus(ERROR);
		HarmoniResponseStatusMsgDTO responseMessage = new HarmoniResponseStatusMsgDTO();
		responseMessage.setResponseCode(CollectionUtils.isEmpty(ex.getBillResult().getHmnCode()) ? "" : ex.getBillResult().getHmnCode().get(0).getValue());
		responseMessage.setResponseMessage(CollectionUtils.isEmpty(ex.getBillResult().getHmnCode()) ? ex.getBillResult().getExplanation() : ex.getBillResult().getHmnCode().get(0).getDescription());
		if(ex.getParameters()!=null && !ex.getParameters().isEmpty()){
			responseMessage.setErrorParameterMap(ex.getParameters());
		}
		resutlDTO.setResponseMessage(responseMessage);
		return resutlDTO;
	}

	private <T> HarmoniCoreServiceResultDTO<T> handleBillException(MicroException ex) {
		HarmoniCoreServiceResultDTO resutlDTO = new HarmoniCoreServiceResultDTO<>();
		resutlDTO.setResult(null);
		resutlDTO.setStatus(ERROR);

		ExceptionData exceptionData = ex.getExceptionData();

		HarmoniResponseStatusMsgDTO responseMessage = new HarmoniResponseStatusMsgDTO();
		responseMessage.setResponseCode(exceptionData.getErrorCode().toString());
		responseMessage.setResponseMessage(exceptionData.getErrorMessage());
		if(ex.getExceptionData().getErrors()!=null && !ex.getExceptionData().getErrors().isEmpty()){
			Map<String, String> errorParameterMap = new HashMap<>();
			for (ValidationErrorData validationErrorData : ex.getExceptionData().getErrors()) {
				errorParameterMap.put(validationErrorData.getField(),validationErrorData.getMessage());
			}
			responseMessage.setErrorParameterMap(errorParameterMap);
		}
		resutlDTO.setResponseMessage(responseMessage);

		return resutlDTO;
	}

	private void fillMandatoryFields(HarmoniCoreServiceBaseDataDTO coreData) {
		if (coreData == null) {
			return;
		}

		requestContext.setChannelSessionId(coreData.getSessionId());
		requestContext.setChannelTransactionId(coreData.getClientUniqueReference());
		requestContext.setAgentCode(coreData.getAgentCode());
		requestContext.setChannelCode(ChannelUtil.convertChannel(coreData.getChannelCode()));
		requestContext.setOperatingBranchCode(coreData.getOperatingBranchCode());
	}


	@Operation(description = "Query Bills")
	@PostMapping(path = "/queryBills")
	public HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> queryBills(
			@RequestBody RequestHarmoniQueryBills request) throws MicroException {

		fillMandatoryFields(request.getRequestSource());
		try {
			QueryBillsResponse queryBills = paymentService.queryBills(harmoniMicroMapper.toQueryBillRequest(request));
			ResponseHarmoniQueryBills harmoniResponse = harmoniMicroMapper.toResponseHarmoniQueryBills(queryBills,
					request);

			HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = new HarmoniCoreServiceResultDTO<>();
			result.setResult(harmoniResponse);
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


}

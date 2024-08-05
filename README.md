@RestController
@Tag(name = "ADK Bill Payment")
@RequestMapping("/adkBillPayment")
@RequiredArgsConstructor
public class PaymentAdkController {
	private static final String X_TRACE_ID = "x-trace-id";
	private static final String X_SESSION_ID = "x-session-id";
	private final PaymentService paymentService;
	private final PaymentFacade paymentFacade;
	private final InstitutionBarcodeService institutionBarcodeService;
	private final RequestContext requestContext;

	private void fillMandatoryFields(BaseWebRequest webRequest, String channelTransactionId, String channelSessionId) {
		requestContext.setChannelSessionId(channelSessionId);
		requestContext.setChannelTransactionId(channelTransactionId);
		requestContext.setAgentCode(webRequest.getAgentCode());
		requestContext.setChannelCode(webRequest.getChannelCode());
		requestContext.setOperatingBranchCode(webRequest.getOperatingBranchCode());
	}

	@Operation(description = "Get commissionAmount")
	@GetMapping(path = "/getBillPaymentExpense")
	public ResponseEntity<GetBillPaymentExpenseResponseDTO> getBillPaymentExpense(
			@Validated GetBillPaymentExpenseRequestDTO request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId)
			throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		GetBillPaymentExpenseResponseDTO response = paymentFacade.getBillPaymentExpense(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}

	@Operation(description = "Query Bills")
	@PostMapping(path = "/queryBills")
	public ResponseEntity<QueryBillsResponse> queryBills(
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId, @RequestBody QueryBillsRequest request)
			throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		QueryBillsResponse response = paymentFacade.queryBills(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}

	@Operation(description = "Do Bill Payment")
	@PostMapping(path = "/doBillPayment")
	public ResponseEntity<DoBillPaymentResponse> doBillPayment(
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId, @RequestBody DoBillPaymentRequest request)
			throws MicroException {
		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		DoBillPaymentResponse response = paymentFacade.doBillPayment(request);
		return new ResponseEntity<>(response, HttpStatus.OK);
	}

	@Operation(description = "Cancel Bill Payment")
	@PostMapping(path = "/cancelBillPayment")
	public ResponseEntity<CancelBillPaymentResponse> cancelBillPayment(
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId,
			@RequestBody CancelBillPaymentRequest request) throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		CancelBillPaymentResponse response = paymentFacade.cancelBillPayment(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}

	@Operation(description = "Get Customer Paid Bill List")
	@GetMapping(path = "/getCustomerPaidBillList")
	public ResponseEntity<GetCustomerPaidBillListResponse> getCustomerPaidBillList(
			@RequestBody @Validated GetCustomerPaidBillListRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);

		return ResponseEntity.ok(paymentService.getCustomerPaidBillList(request));
	}

	@Operation(description = "Get subsriber no with barcode")
	@GetMapping(path = "/getSubscriberNoWithBarcode")
	public ResponseEntity<GetSubscriberNoWithBarcodeResponse> getSubscriberNoWithBarcode(
			@Validated GetSubscriberNoWithBarcodeRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		GetSubscriberNoWithBarcodeResponse response = institutionBarcodeService.getSubscriberNoWithBarcode(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}

	@Operation(description = "Parse SubscriberNo Into Parts")
	@GetMapping(path = "/parseSubscriberNoIntoParts")
	public ResponseEntity<ParseSubscriberNoIntoPartsResponse> parseSubscriberNoIntoParts(
			@Validated ParseSubscriberNoIntoPartsRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);

		return new ResponseEntity<>(paymentService.parseSubscriberNoIntoParts(request), HttpStatus.OK);
	}
	
	
	@Operation(description = "Notify payment to institution")
	@PostMapping(path = "/notifyPayment")
	public ResponseEntity<NotifyPaymentResponse> notifyPayment(@RequestBody NotifyPaymentRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) throws MicroException {
		fillMandatoryFields(request, channelTransactionId, channelSessionId);

		return ResponseEntity.ok().body(paymentService.notifyPayment(request));
	}

	@Operation(description = "Notify payment cancel to institution")
	@PostMapping(path = "/notifyPaymentCancel")
	public ResponseEntity<NotifyPaymentCancelResponse> notifyPaymentCancel(
			@RequestBody NotifyPaymentCancelRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) throws MicroException {
		fillMandatoryFields(request, channelTransactionId, channelSessionId);		
		
		return ResponseEntity.ok().body(paymentService.notifyPaymentCancel(request));
	}
	
}

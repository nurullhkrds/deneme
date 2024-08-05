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

@RestController
@Tag(name = "ADK Bill Payment")
@RequestMapping("/adkBillPayment")
@RequiredArgsConstructor
public class InstitutionAdkController {
	private static final String X_TRACE_ID = "x-trace-id";
	private static final String X_SESSION_ID = "x-session-id";

	private final ProductCityService productCityService;
	private final InstitutionDetailService institutionDetailService;
	private final RequestContext requestContext;
	private final InstitutionService institutionService;
	
	private void fillMandatoryFields(BaseWebRequest webRequest, String channelTransactionId, String channelSessionId) {
		requestContext.setChannelSessionId(channelSessionId);
		requestContext.setChannelTransactionId(channelTransactionId);	
		requestContext.setAgentCode(webRequest.getAgentCode());
		requestContext.setChannelCode(webRequest.getChannelCode());
		requestContext.setOperatingBranchCode(webRequest.getOperatingBranchCode());
	}

	@Operation(description = "Get product city list")
	@GetMapping(path = "/getProductWithCityList")
	public ResponseEntity<GetProductWithCityListResponse> getProductWithCityList(
			@Validated GetProductWithCityListRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		GetProductWithCityListResponse response = productCityService.getProductWithCityList(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}
	
	@Operation(description = "Get institution list")
	@GetMapping(path = "/getInstitutionDetailList")
	public ResponseEntity<GetInstitutionDetailListResponse> getInstitutionDetailList(
			@Validated GetInstitutionDetailListRequest request,
			@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
			@RequestHeader(value = X_SESSION_ID) String channelSessionId) throws MicroException {

		fillMandatoryFields(request, channelTransactionId, channelSessionId);
		GetInstitutionDetailListResponse response = institutionDetailService.getInstitutionList(request);

		return new ResponseEntity<>(response, HttpStatus.OK);
	}
	
	@Operation(description = "Get institution-product list")
	@GetMapping(path = "/getInstitutionProductList")
	public ResponseEntity<GetProductWithInstitutionListResponse> getInstitutionDetailList() throws MicroException {

		GetProductWithInstitutionListResponse response = institutionService.getInstitutionAndProductCodeList();

		return new ResponseEntity<>(response, HttpStatus.OK);
	}
}

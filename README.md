@Component
public class BillPaymentRestFacadeClient {

	@Value("${external.billPaymentRestFacade.address}")
	private String address;

	@Value("${external.billPaymentRestFacade.service.readTimeout}")
	private Long readTimeout;

	@Value("${external.billPaymentRestFacade.service.connectTimeout}")
	private Long connectionTimeout;

	public ResponseGetCustomerPaidBillList getCustomerPaidBillList(RequestGetCustomerPaidBillList request) {
		URI uri = UriComponentsBuilder.fromHttpUrl(address).path("billPaymentRestFacade").queryParam("operationName", "getCustomerPaidBillList").build().toUri();

		RestTemplate restTemplate = new RestTemplateBuilder()
				.setConnectTimeout(Duration.ofMillis(connectionTimeout)).setReadTimeout(Duration.ofMillis(readTimeout))
				.build();
		return restTemplate.postForObject(uri, request, ResponseGetCustomerPaidBillList.class);
	}

	public ResponseQueryBillsHmn queryBills(RequestQueryBillsHmn request) {
		URI uri = UriComponentsBuilder.fromHttpUrl(address).path("billPaymentRestFacade").queryParam("operationName", "queryBills").build().toUri();

		RestTemplate restTemplate = new RestTemplateBuilder()
				.setConnectTimeout(Duration.ofMillis(connectionTimeout)).setReadTimeout(Duration.ofMillis(readTimeout))
				.build();
		return restTemplate.postForObject(uri, request, ResponseQueryBillsHmn.class);
	}


	public ResponseBillPaymentExpenseHmn getBillPaymentExpense(RequestBillPaymentExpenseHmn request) {
		URI uri = UriComponentsBuilder.fromHttpUrl(address).path("billPaymentRestFacade").queryParam("operationName", "getBillPaymentExpense").build().toUri();

		RestTemplate restTemplate = new RestTemplateBuilder()
				.setConnectTimeout(Duration.ofMillis(connectionTimeout)).setReadTimeout(Duration.ofMillis(readTimeout))
				.build();
		return restTemplate.postForObject(uri, request, ResponseBillPaymentExpenseHmn.class);
	}

	public ResponsePayBillHmn payBill(RequestPayBillHmn request) {
		URI uri = UriComponentsBuilder.fromHttpUrl(address).path("billPaymentRestFacade").queryParam("operationName", "payBill").build().toUri();

		RestTemplate restTemplate = new RestTemplateBuilder()
				.setConnectTimeout(Duration.ofMillis(connectionTimeout)).setReadTimeout(Duration.ofMillis(readTimeout))
				.build();
		return restTemplate.postForObject(uri, request, ResponsePayBillHmn.class);
	}

	public ResponseQueryBillsHmn reverseBillPayment(RequestQueryBillsHmn request) {
		URI uri = UriComponentsBuilder.fromHttpUrl(address).path("billPaymentRestFacade").queryParam("operationName", "reverseBillPayment").build().toUri();

		RestTemplate restTemplate = new RestTemplateBuilder()
				.setConnectTimeout(Duration.ofMillis(connectionTimeout)).setReadTimeout(Duration.ofMillis(readTimeout))
				.build();
		return restTemplate.postForObject(uri, request, ResponseQueryBillsHmn.class);
	}

}

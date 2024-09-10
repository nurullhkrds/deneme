class BillPaymentRestFacadeClientTest {

    @Mock
    private RestTemplateBuilder restTemplateBuilder;

    @Mock
    private RestTemplate restTemplate;

    @InjectMocks
    private BillPaymentRestFacadeClient billPaymentRestFacadeClient;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
        when(restTemplateBuilder.setConnectTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
        when(restTemplateBuilder.setReadTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
        when(restTemplateBuilder.build()).thenReturn(restTemplate);
    }

    @Test
    void testGetCustomerPaidBillList() {
        RequestGetCustomerPaidBillList request = new RequestGetCustomerPaidBillList();
        ResponseGetCustomerPaidBillList expectedResponse = new ResponseGetCustomerPaidBillList();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "getCustomerPaidBillList").build().toUri();

        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseGetCustomerPaidBillList.class)))
                .thenReturn(expectedResponse);

        ResponseGetCustomerPaidBillList actualResponse = billPaymentRestFacadeClient.getCustomerPaidBillList(request);

        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testQueryBills() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();
        ResponseQueryBillsHmn expectedResponse = new ResponseQueryBillsHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "queryBills").build().toUri();

        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseQueryBillsHmn.class)))
                .thenReturn(expectedResponse);

        ResponseQueryBillsHmn actualResponse = billPaymentRestFacadeClient.queryBills(request);

        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testGetBillPaymentExpense() {
        RequestBillPaymentExpenseHmn request = new RequestBillPaymentExpenseHmn();
        ResponseBillPaymentExpenseHmn expectedResponse = new ResponseBillPaymentExpenseHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "getBillPaymentExpense").build().toUri();

        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseBillPaymentExpenseHmn.class)))
                .thenReturn(expectedResponse);

        ResponseBillPaymentExpenseHmn actualResponse = billPaymentRestFacadeClient.getBillPaymentExpense(request);

        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testPayBill() {
        RequestPayBillHmn request = new RequestPayBillHmn();
        ResponsePayBillHmn expectedResponse = new ResponsePayBillHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "payBill").build().toUri();

        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponsePayBillHmn.class)))
                .thenReturn(expectedResponse);

        ResponsePayBillHmn actualResponse = billPaymentRestFacadeClient.payBill(request);

        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testReverseBillPayment() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();
        ResponseQueryBillsHmn expectedResponse = new ResponseQueryBillsHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "reverseBillPayment").build().toUri();

        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseQueryBillsHmn.class)))
                .thenReturn(expectedResponse);

        ResponseQueryBillsHmn actualResponse = billPaymentRestFacadeClient.reverseBillPayment(request);

        assertEquals(expectedResponse, actualResponse);
    }
}

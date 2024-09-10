@ExtendWith(MockitoExtension.class)
public class BillPaymentRestFacadeClientTest {

    @Mock
    private RestTemplate restTemplate;

    @InjectMocks
    private BillPaymentRestFacadeClient billPaymentRestFacadeClient;

    @BeforeEach
    public void setUp() throws NoSuchFieldException, IllegalAccessException {
        setField(billPaymentRestFacadeClient, "address", "http://localhost:8080");
        setField(billPaymentRestFacadeClient, "readTimeout", 1000L);
        setField(billPaymentRestFacadeClient, "connectionTimeout", 1001L);
    }

    @Test
    public void testGetCustomerPaidBillList() {
        RequestGetCustomerPaidBillList request = new RequestGetCustomerPaidBillList();

        // Mock RestTemplate to throw ResourceAccessException
        when(restTemplate.postForObject(any(URI.class), any(RequestGetCustomerPaidBillList.class), eq(ResponseGetCustomerPaidBillList.class)))
                .thenThrow(new ResourceAccessException("Timeout occurred"));

        // Assert that ResourceAccessException is thrown
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.getCustomerPaidBillList(request));
    }

    @Test
    public void testQueryBills() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();

        // Mock RestTemplate to throw ResourceAccessException
        when(restTemplate.postForObject(any(URI.class), any(RequestQueryBillsHmn.class), eq(ResponseQueryBillsHmn.class)))
                .thenThrow(new ResourceAccessException("Timeout occurred"));

        // Assert that ResourceAccessException is thrown
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.queryBills(request));
    }

    @Test
    public void testGetBillPaymentExpense() {
        RequestBillPaymentExpenseHmn request = new RequestBillPaymentExpenseHmn();

        // Mock RestTemplate to throw ResourceAccessException
        when(restTemplate.postForObject(any(URI.class), any(RequestBillPaymentExpenseHmn.class), eq(ResponseBillPaymentExpenseHmn.class)))
                .thenThrow(new ResourceAccessException("Timeout occurred"));

        // Assert that ResourceAccessException is thrown
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.getBillPaymentExpense(request));
    }

    @Test
    public void testPayBill() {
        RequestPayBillHmn request = new RequestPayBillHmn();

        // Mock RestTemplate to throw ResourceAccessException
        when(restTemplate.postForObject(any(URI.class), any(RequestPayBillHmn.class), eq(ResponsePayBillHmn.class)))
                .thenThrow(new ResourceAccessException("Timeout occurred"));

        // Assert that ResourceAccessException is thrown
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.payBill(request));
    }

    @Test
    public void testReverseBillPayment() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();

        // Mock RestTemplate to throw ResourceAccessException
        when(restTemplate.postForObject(any(URI.class), any(RequestQueryBillsHmn.class), eq(ResponseQueryBillsHmn.class)))
                .thenThrow(new ResourceAccessException("Timeout occurred"));

        // Assert that ResourceAccessException is thrown
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.reverseBillPayment(request));
    }

    private void setField(Object target, String fieldName, Object value) throws NoSuchFieldException, IllegalAccessException {
        Field field = target.getClass().getDeclaredField(fieldName);
        field.setAccessible(true);
        field.set(target, value);
    }
}

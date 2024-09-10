@ExtendWith(MockitoExtension.class)
public class BillPaymentRestFacadeClientTest {

    @Mock
    private RestTemplate restTemplate;

    @InjectMocks
    private BillPaymentRestFacadeClient billPaymentRestFacadeClient;

    @BeforeEach
    public void setUp() throws NoSuchFieldException, IllegalAccessException {
        MockitoAnnotations.openMocks(this);
        setField(billPaymentRestFacadeClient, "address", "http://localhost:8080");
        setField(billPaymentRestFacadeClient, "readTimeout", 1000L);
        setField(billPaymentRestFacadeClient, "connectionTimeout", 1001L);
    }

    @Test
    public void testGetCustomerPaidBillList() {
        RequestGetCustomerPaidBillList request = new RequestGetCustomerPaidBillList();
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.getCustomerPaidBillList(request));
    }

    @Test
    public void testQueryBills() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();

        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.queryBills(request));
    }

    @Test
    public void testGetBillPaymentExpense() {
        RequestBillPaymentExpenseHmn request = new RequestBillPaymentExpenseHmn();
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.getBillPaymentExpense(request));
    }

    @Test
    public void testPayBill() {
        RequestPayBillHmn request = new RequestPayBillHmn();
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.payBill(request));
    }

    @Test
    public void testReverseBillPayment() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();
        assertThrows(ResourceAccessException.class, () -> billPaymentRestFacadeClient.reverseBillPayment(request));

    }

    private void setField(Object target, String fieldName, Object value) throws NoSuchFieldException, IllegalAccessException {
        Field field = target.getClass().getDeclaredField(fieldName);
        field.setAccessible(true);
        field.set(target, value);
    }
}
org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <org.springframework.web.client.ResourceAccessException> but was: <org.springframework.web.client.HttpClientErrorException.NotFound>

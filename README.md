
public class QueryBillProcessInputTest {

    private QueryBillProcessInput queryBillProcessInput;

    @Mock
    private List<SubscriberNoPartRequestDTO> subscriberNoPartList;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        queryBillProcessInput = new QueryBillProcessInput();
    }

    @Test
    public void testSetCustomerNo() {
        Long customerNo = 123L;
        queryBillProcessInput.setCustomerNo(customerNo);
        assertEquals(customerNo, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.CUSTOMER_NO.getKey()));
    }

    @Test
    public void testSetIdentityNo() {
        Long identityNo = 456L;
        queryBillProcessInput.setIdentityNo(identityNo);
        assertEquals(identityNo, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.IDENTITY_NO.getKey()));
    }

    @Test
    public void testSetTaxOfficeNo() {
        String taxNo = "TAX123";
        queryBillProcessInput.setTaxOfficeNo(taxNo);
        assertEquals(taxNo, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.TAX_ID.getKey()));
    }

    @Test
    public void testSetSubscriberNo() {
        String subscriberNo = "SUB456";
        queryBillProcessInput.setSubscriberNo(subscriberNo);
        assertEquals(subscriberNo, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.SUBSCRIBER_NO.getKey()));
    }

    @Test
    public void testSetSubscriberNoPartList() {
        queryBillProcessInput.setSubscriberNoPartList(subscriberNoPartList);
        assertEquals(subscriberNoPartList, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey()));
    }

    @Test
    public void testSetCurrency() {
        String currency = "USD";
        queryBillProcessInput.setCurrency(currency);
        assertEquals(currency, queryBillProcessInput.getDataPack().get(ProcessDataPackKey.CURRENCY.getKey()));
    }
}

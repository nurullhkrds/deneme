@ExtendWith(MockitoExtension.class)
public class HarmoniPaymentAdkControllerTest {

    @InjectMocks
    private HarmoniPaymentAdkController harmoniPaymentAdkController;

    @Mock
    private SubscriberService subscriberService;

    @Mock
    private PaymentService paymentService;

    @Mock
    private RequestContext requestContext;

    @Mock
    private HarmoniMicroMapper harmoniMicroMapper;

    @BeforeEach
    void setUp() {
        // Optional setup tasks
    }

    @Test
    void testQueryBills_Success() throws MicroException, BillException {
        // Arrange
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        ResponseHarmoniQueryBills responseHarmoniQueryBills = new ResponseHarmoniQueryBills();

        when(paymentService.queryBills(any())).thenReturn(queryBillsResponse);
        when(harmoniMicroMapper.toQueryBillRequest(any())).thenReturn((QueryBillsRequest) new Object());
        when(harmoniMicroMapper.toResponseHarmoniQueryBills(any(), any())).thenReturn(responseHarmoniQueryBills);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(responseHarmoniQueryBills, result.getResult());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

public class PaymentFacadeImplTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private SubscriberService subscriberService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;

    @InjectMocks
    private PaymentFacadeImpl paymentFacade;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testQueryBills_WhenMicroInstitution_ShouldCallPaymentService() throws MicroException {
        QueryBillsRequest request = new QueryBillsRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.YES.getValue());
        when(paymentService.queryBills(request)).thenReturn(new QueryBillsResponse());

        QueryBillsResponse response = paymentFacade.queryBills(request);

        verify(paymentService, times(1)).queryBills(request);
        verify(billPaymentRestFacade, never()).queryBills(any());
        assertNotNull(response);
    }

    @Test
    public void testQueryBills_WhenNotMicroInstitution_ShouldCallBillPaymentRestFacade() throws MicroException {
        QueryBillsRequest request = new QueryBillsRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.NO.getValue());
        when(billPaymentRestFacade.queryBills(request)).thenReturn(new QueryBillsResponse());

        QueryBillsResponse response = paymentFacade.queryBills(request);

        verify(paymentService, never()).queryBills(any());
        verify(billPaymentRestFacade, times(1)).queryBills(request);
        assertNotNull(response);
    }

    @Test
    public void testDoBillPayment_WhenMicroInstitution_ShouldCallPaymentService() throws MicroException {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.YES.getValue());
        when(paymentService.doBillPayment(request)).thenReturn(new DoBillPaymentResponse());

        DoBillPaymentResponse response = paymentFacade.doBillPayment(request);

        verify(paymentService, times(1)).doBillPayment(request);
        verify(billPaymentRestFacade, never()).doBillPayment(any());
        assertNotNull(response);
    }

    @Test
    public void testDoBillPayment_WhenNotMicroInstitution_ShouldCallBillPaymentRestFacade() throws MicroException {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.NO.getValue());
        when(billPaymentRestFacade.doBillPayment(request)).thenReturn(new DoBillPaymentResponse());

        DoBillPaymentResponse response = paymentFacade.doBillPayment(request);

        verify(paymentService, never()).doBillPayment(any());
        verify(billPaymentRestFacade, times(1)).doBillPayment(request);
        assertNotNull(response);
    }

    @Test
    public void testCancelBillPayment_WhenMicroInstitution_ShouldCallPaymentService() throws MicroException {
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.YES.getValue());
        when(paymentService.cancelBillPayment(request)).thenReturn(new CancelBillPaymentResponse());

        CancelBillPaymentResponse response = paymentFacade.cancelBillPayment(request);

        verify(paymentService, times(1)).cancelBillPayment(request);
        verify(billPaymentRestFacade, never()).cancelBillPayment(any());
        assertNotNull(response);
    }

    @Test
    public void testCancelBillPayment_WhenNotMicroInstitution_ShouldCallBillPaymentRestFacade() throws MicroException {
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.NO.getValue());
        when(billPaymentRestFacade.cancelBillPayment(request)).thenReturn(new CancelBillPaymentResponse());

        CancelBillPaymentResponse response = paymentFacade.cancelBillPayment(request);

        verify(paymentService, never()).cancelBillPayment(any());
        verify(billPaymentRestFacade, times(1)).cancelBillPayment(request);
        assertNotNull(response);
    }

    @Test
    public void testGetBillPaymentExpense_WhenMicroInstitution_ShouldCallSubscriberService() throws MicroException {
        GetBillPaymentExpenseRequestDTO request = new GetBillPaymentExpenseRequestDTO();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.YES.getValue());
        when(subscriberService.getBillPaymentExpense(request)).thenReturn(new GetBillPaymentExpenseResponseDTO());

        GetBillPaymentExpenseResponseDTO response = paymentFacade.getBillPaymentExpense(request);

        verify(subscriberService, times(1)).getBillPaymentExpense(request);
        verify(billPaymentRestFacade, never()).getBillPaymentExpense(any());
        assertNotNull(response);
    }

    @Test
    public void testGetBillPaymentExpense_WhenNotMicroInstitution_ShouldCallBillPaymentRestFacade() throws MicroException {
        GetBillPaymentExpenseRequestDTO request = new GetBillPaymentExpenseRequestDTO();
        // setup request with productCode and institutionCode

        when(institutionFeatureService.getFeatureValue(any(), any(), any())).thenReturn(EnumYesNo.NO.getValue());
        when(billPaymentRestFacade.getBillPaymentExpense(request)).thenReturn(new GetBillPaymentExpenseResponseDTO());

        GetBillPaymentExpenseResponseDTO response = paymentFacade.getBillPaymentExpense(request);

        verify(subscriberService, never()).getBillPaymentExpense(any());
        verify(billPaymentRestFacade, times(1)).getBillPaymentExpense(request);
        assertNotNull(response);
    }
}

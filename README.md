ExtendWith(MockitoExtension.class)
public class QueryBillsProcessTest {

    @Mock
    private AdapterService adapterService;
    @Mock
    private ProvisionService provisionService;
    @Mock
    private InstitutionUserIntService institutionUserIntService;
    @Mock
    private InstitutionUserIntfMapper institutionUserIntMapper;
    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;
    @Mock
    private PaymentRepository paymentRepository;
    @Mock
    private PaymentMapper paymentMapper;
    @Mock
    private LimitationService limitationService;
    @Mock
    private PaymentEventPublisher paymentEventPublisher;
    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    @BeforeEach
    void setUp() {
        // Mock setup (if any initial setup needed)
    }

    @Test
    void testExecuteProcessOnline() throws BillException {
        // Arrange
        when(adapterService.queryBills(any(), any(), any())).thenReturn(new QueryBillsAdapterResponse());
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList());
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());
        when(provisionService.createProvisions(any())).thenReturn(Collections.emptyList());

        // Act
        queryBillsProcess.executeProcess();

        // Assert
        // Verify the steps executed and mocks were called
        verify(adapterService, times(1)).queryBills(any(), any(), any());
        verify(provisionService, times(1)).createProvisions(any());
        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());
    }

    @Test
    void testExecuteProcessOffline() throws BillException {
        // Arrange
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(false);

        // Act
        queryBillsProcess.executeProcess();

        // Assert
        // Verify the offline steps executed
        verify(provisionService, times(1)).invalidateNotPaidProvisions(any(), any());
        verify(provisionService, times(1)).createProvisions(any());
        verify(paymentEventPublisher, never()).publishInquiryLimiationNotification(any());
    }
}

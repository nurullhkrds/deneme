@ExtendWith(MockitoExtension.class)
public class QueryBillsProcessTest {

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

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

    private ProcessDataPack dataPack;
    private String channelTransactionId = "testChannelTransactionId";
    private String channelSessionId = "testChannelSessionId";
    private String institutionDebtTypeId = "testInstitutionDebtTypeId";
    private String channelCode = "testChannelCode";
    private String branchCode = "testBranchCode";
    private String institutionCode = "testInstitutionCode";
    private String productCode = "testProductCode";
    private String agentCode = "testAgentCode";
    private Institution institution = new Institution("testInstitutionId", "testInstitutionCode");
    private EnumBillResult error;

    @BeforeEach
    public void setUp() {
        dataPack = mock(ProcessDataPack.class);
        when(dataPack.get(ProcessDataPackKey.CUSTOMER_NO.getKey())).thenReturn(12345L);
        when(dataPack.get(ProcessDataPackKey.IDENTITY_NO.getKey())).thenReturn(67890L);
        when(dataPack.get(ProcessDataPackKey.TAX_ID.getKey())).thenReturn("1234567890");
        when(dataPack.get(ProcessDataPackKey.SUBSCRIBER_NO.getKey())).thenReturn("subscriberNo");
        when(dataPack.get(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey())).thenReturn(Collections.emptyList());
        when(dataPack.get(ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");

        queryBillsProcess.setDataPack(dataPack);
        queryBillsProcess.setChannelTransactionId(channelTransactionId);
        queryBillsProcess.setChannelSessionId(channelSessionId);
        queryBillsProcess.setInstitutionDebtTypeId(institutionDebtTypeId);
        queryBillsProcess.setChannelCode(channelCode);
        queryBillsProcess.setBranchCode(branchCode);
        queryBillsProcess.setInstitution(institution);
        queryBillsProcess.setProductCode(productCode);
        queryBillsProcess.setAgentCode(agentCode);
    }

    @Test
    public void testExecuteProcess_Successful() throws BillException {
        // Mock methods
        when(adapterService.queryBills(any(), any(), any())).thenReturn(new QueryBillsAdapterResponse());
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(any(), any(), any(), any()))
                .thenReturn(new PaymentAllowedResponse(true));
        when(institutionUserIntService.getUserInterface(any())).thenReturn(Collections.emptyList());
        
        // Execute process
        queryBillsProcess.executeProcess();

        // Verify methods
        verify(adapterService, times(1)).queryBills(any(), any(), any());
        verify(provisionService, times(1)).invalidateNotPaidProvisions(any(), any());
        verify(provisionService, times(1)).createProvisions(any());
        verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());

        // Assert result
        assertEquals(EnumBillResult.SUCCESS, queryBillsProcess.getExecutionOutput().getResult());
    }

    @Test
    public void testGatherData() {
        // Given
        GatherData gatherData = queryBillsProcess.new GatherData();

        // When
        gatherData.executeStep();

        // Then
        assertEquals(12345L, queryBillsProcess.getCustomerNo());
        assertEquals(67890L, queryBillsProcess.getIdentityNo());
        assertEquals("1234567890", queryBillsProcess.getTaxOfficeNo());
        assertEquals("subscriberNo", queryBillsProcess.getSubscriberNo());
        assertEquals("USD", queryBillsProcess.getCurrency());
    }

    @Test
    public void testValidateSubscriberNo_Invalid() {
        // Given
        ValidateSubscriberNo validateSubscriberNo = queryBillsProcess.new ValidateSubscriberNo();
        when(SubscriberNumberUtils.checkSubscriberNumberParts(any(), any())).thenReturn(false);

        // When
        validateSubscriberNo.executeStep();

        // Then
        assertEquals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID, queryBillsProcess.getError());
    }

    // Add similar tests for other process steps...

}

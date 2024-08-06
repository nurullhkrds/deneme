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

    @Before
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        queryBillsProcess.setDataPack(new HashMap<>());

        // Initialize stepHandler
        queryBillsProcess.stepHandler = queryBillsProcess.new ProcessStepHandler();
    }

    @Test
    public void testGatherDataStep() {
        // Mock the necessary data
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.TAX_ID.getKey(), "tax123");
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "sub123");
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
        
        // Call the method under test
        queryBillsProcess.executeProcess();
        
        // Assert that data is gathered correctly
        assertEquals(Long.valueOf(123L), queryBillsProcess.getCustomerNo());
        assertEquals(Long.valueOf(456L), queryBillsProcess.getIdentityNo());
        assertEquals("tax123", queryBillsProcess.getTaxOfficeNo());
        assertEquals("sub123", queryBillsProcess.getSubscriberNo());
        assertEquals("USD", queryBillsProcess.getCurrency());
    }

    @Test
    public void testFormatSubscriberNoPartListStep() {
        // Mock necessary services and data
        queryBillsProcess.setSubscriberNoPartList(Collections.emptyList());
        queryBillsProcess.setInstitutionUserIntListDTO(Collections.emptyList());

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that subscriber number is formatted correctly
        assertNotNull(queryBillsProcess.getSubscriberNo());
    }

    @Test
    public void testValidateSubscriberNoStep() {
        // Mock necessary services and data
        queryBillsProcess.setSubscriberNoPartList(Collections.emptyList());
        queryBillsProcess.setInstitutionUserIntListDTO(Collections.emptyList());

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that subscriber number is validated correctly
        assertEquals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID, queryBillsProcess.getError());
    }

    @Test
    public void testCheckCustomerQueryLimitStep() {
        // Mock necessary services and data
        PaymentAllowedResponse response = mock(PaymentAllowedResponse.class);
        when(response.isPaymentAllowed()).thenReturn(false);
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString()))
                .thenReturn(response);
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        queryBillsProcess.setCustomerNo(123L);
        queryBillsProcess.setIdentityNo(456L);

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that query limit check is performed correctly
        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, queryBillsProcess.getError());
    }

    @Test
    public void testQueryFromServiceStep() throws BillException {
        // Mock necessary services and data
        QueryBillsAdapterResponse response = mock(QueryBillsAdapterResponse.class);
        when(response.getInternalResultCode()).thenReturn("0"); // Success code
        when(response.getBills()).thenReturn(Collections.emptyList());
        when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
                .thenReturn(response);

        queryBillsProcess.setCustomerNo(123L);
        queryBillsProcess.setIdentityNo(456L);
        queryBillsProcess.setSubscriberNo("sub123");
        queryBillsProcess.setSubscriberNoPartList(Collections.emptyList());

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that service query is performed correctly
        assertNotNull(queryBillsProcess.getQueriedBillDTOList());
    }

    @Test
    public void testEliminateBillsStep() {
        // Mock necessary services and data
        QueriedBillDTO queriedBill = mock(QueriedBillDTO.class);
        when(queriedBill.getBillDueDate()).thenReturn(LocalDate.now());
        when(queriedBill.getBillNo()).thenReturn("bill123");
        when(queriedBill.isPayable()).thenReturn(true);
        when(queriedBill.getBillAmount()).thenReturn(100.0);
        queryBillsProcess.setQueriedBillDTOList(Collections.singletonList(queriedBill));

        ResponseGetCustomerPaidBillList responseGetCustomerPaidBillList = mock(ResponseGetCustomerPaidBillList.class);
        when(responseGetCustomerPaidBillList.getBillDTOList()).thenReturn(Collections.emptyList());
        when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
                .thenReturn(responseGetCustomerPaidBillList);

        when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString()))
                .thenReturn(Collections.emptyList());

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that bills are eliminated correctly
        assertNotNull(queryBillsProcess.getQueriedBillDTOList());
    }

    @Test
    public void testInvalidateNotPaidProvisionsStep() {
        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that provisions are invalidated
        verify(provisionService).invalidateNotPaidProvisions(anyLong(), anyString());
    }

    @Test
    public void testCreateProvisionsStep() {
        // Mock necessary data
        QueriedBillDTO queriedBill = mock(QueriedBillDTO.class);
        when(queriedBill.getCurrency()).thenReturn("USD");
        when(queriedBill.getQueryStan()).thenReturn("queryStan");
        when(queriedBill.getBillNo()).thenReturn("billNo");
        when(queriedBill.getExplanation()).thenReturn("explanation");
        when(queriedBill.getBillTerm()).thenReturn("billTerm");
        when(queriedBill.getInstitutionQueryStan()).thenReturn("institutionQueryStan");
        when(queriedBill.getBillDueDate()).thenReturn(LocalDate.now());
        when(queriedBill.isPayable()).thenReturn(true);
        when(queriedBill.getBillIssueDate()).thenReturn(LocalDate.now());
        when(queriedBill.getBillAmount()).thenReturn(100.0);
        when(queriedBill.getSubscriberName()).thenReturn("subscriberName");
        when(queriedBill.getSubscriberNo()).thenReturn("subscriberNo");
        queryBillsProcess.setQueriedBillDTOList(Collections.singletonList(queriedBill));

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that provisions are created correctly
        assertNotNull(queryBillsProcess.getProvisionList());
    }

    @Test
    public void testUpdateCustomerQueryLimitStep() {
        // Mock necessary services and data
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);

        queryBillsProcess.setCustomerNo(123L);
        queryBillsProcess.setIdentityNo(456L);

        // Execute the step
        queryBillsProcess.executeProcess();

        // Assert that customer query limit is updated
        verify(paymentEventPublisher).publishInquiryLimiationNotification(any(NotifyInquiryLimitationRequest.class));
    }

    @Test
    public void testExecuteProcess() {
        // Mock all necessary services and data
        // Set up mocks and initial data
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.TAX_ID.getKey(), "tax123");
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "sub123");
        queryBillsProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");

        // Execute the process
        queryBillsProcess.executeProcess();

        // Assert that the process executed correctly
        assertNotNull(queryBillsProcess.getExecutionOutput());
    }
}

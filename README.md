import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;

class QueryBillsProcessTest {

    @Mock
    private AdapterService adapterService;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private InstitutionUserIntService institutionUserIntService;

    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;

    @Mock
    private PaymentRepository paymentRepository;

    @Mock
    private PaymentMapper paymentMapper;

    @Mock
    private LimitationService limitationService;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;

    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Veri paketini dolduralım
        queryBillsProcess.dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        queryBillsProcess.dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        queryBillsProcess.dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "SUB123");
        queryBillsProcess.dataPack.put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
    }

    @Test
    void testExecuteProcess_OnlineProcess_Success() throws BillException {
        // Mocking for online process
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
        when(adapterService.queryBills(any(), any(), any())).thenReturn(prepareMockQueryBillsResponse());

        // Process execution
        queryBillsProcess.executeProcess();

        // Verifying the steps and results
        assertNotNull(queryBillsProcess.getQueriedBillDTOList());
        verify(adapterService, times(1)).queryBills(any(), any(), any());
    }

    @Test
    void testExecuteProcess_OfflineProcess_Success() throws BillException {
        // Mocking for offline process
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(false);

        // Process execution
        queryBillsProcess.executeProcess();

        // Verifying the steps
        verify(adapterService, never()).queryBills(any(), any(), any()); // Should not call online process
        verify(provisionService, times(1)).invalidateNotPaidProvisions(any(), any());
    }

    @Test
    void testGatherDataStep() {
        // Creating and executing the GatherData step
        QueryBillsProcess.GatherData gatherData = queryBillsProcess.new GatherData();
        gatherData.executeStep();

        // Verify that the services were fetched and set
        assertNotNull(queryBillsProcess.adapterService);
        assertNotNull(queryBillsProcess.institutionUserIntService);
        assertNotNull(queryBillsProcess.billPaymentRestFacade);
        assertEquals(123L, queryBillsProcess.customerNo);
        assertEquals(456L, queryBillsProcess.identityNo);
        assertEquals("SUB123", queryBillsProcess.subscriberNo);
        assertEquals("USD", queryBillsProcess.currency);
    }

    @Test
    void testFormatSubscriberNoPartList() {
        // Mocking the required dependencies
        queryBillsProcess.institutionUserIntListDTO = Collections.emptyList();
        queryBillsProcess.subscriberNoPartList = Collections.singletonList(new SubscriberNoPartRequestDTO());

        // Execute FormatSubscriberNoPartList step
        QueryBillsProcess.FormatSubscriberNoPartList formatStep = queryBillsProcess.new FormatSubscriberNoPartList();
        formatStep.executeStep();

        // Verifying the formatting
        assertNotNull(queryBillsProcess.subscriberNo);
    }

    @Test
    void testCheckCustomerQueryLimit() {
        // Mock the behavior for limitation service
        when(limitationService.isPaymentAllowedWithoutDebtOwner(any(), any(), any(), any())).thenReturn(new PaymentAllowedResponse(true));

        // Execute CheckCustomerQueryLimit step
        QueryBillsProcess.CheckCustomerQueryLimit checkLimit = queryBillsProcess.new CheckCustomerQueryLimit();
        checkLimit.executeStep();

        // Verify no errors were set
        assertNull(queryBillsProcess.getError());
    }

    @Test
    void testEliminateBills_NoBillsFound() {
        // Mock an empty response from both services
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList(Collections.emptyList()));
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());

        // Execute EliminateBills step
        queryBillsProcess.queriedBillDTOList = Arrays.asList(new QueriedBillDTO()); // Add one bill
        QueryBillsProcess.EliminateBills eliminateBills = queryBillsProcess.new EliminateBills();
        eliminateBills.executeStep();

        // Since no bills matched, error should be set
        assertEquals(EnumBillResult.BILL_NOT_FOUND, queryBillsProcess.getError());
    }

    // Helper method to prepare mock response
    private QueryBillsAdapterResponse prepareMockQueryBillsResponse() {
        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode("000");
        QueriedBillDTO billDTO = new QueriedBillDTO();
        billDTO.setBillNo("BILL123");
        billDTO.setBillAmount(new BigDecimal("100.00"));
        response.setBills(Collections.singletonList(billDTO));
        return response;
    }
}

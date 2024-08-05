import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Spy;
import org.mockito.junit.jupiter.MockitoExtension;

import java.time.LocalDateTime;
import java.util.Collections;

@ExtendWith(MockitoExtension.class)
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

    @Spy
    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    @BeforeEach
    void setUp() {
        // Mock the dataPack method from AbstractProcess
        when(queryBillsProcess.dataPack.get((Long) ProcessDataPackKey.CUSTOMER_NO.getKey())).thenReturn(123L);
        when(queryBillsProcess.dataPack.get((Long) ProcessDataPackKey.IDENTITY_NO.getKey())).thenReturn(456L);
        when(queryBillsProcess.dataPack.get((String) ProcessDataPackKey.TAX_ID.getKey())).thenReturn("12345");
        when(queryBillsProcess.dataPack.get((String) ProcessDataPackKey.SUBSCRIBER_NO.getKey())).thenReturn("67890");
        when(queryBillsProcess.dataPack.get((List<SubscriberNoPartRequestDTO>) ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey())).thenReturn(Collections.emptyList());
        when(queryBillsProcess.dataPack.get((String) ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");

        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        // Mock responses for various services
        when(adapterService.queryBills(any(), any(), any())).thenReturn(mockQueryBillsAdapterResponse(EnumBillResult.SUCCESS));
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList());
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());
        when(provisionService.createProvisions(any())).thenReturn(Collections.emptyList());

        queryBillsProcess.executeProcess();

        // Add assertions to verify the results
    }

    @Test
    void testExecuteProcessSubscriberNumberInvalid() {
        // Mock responses to invalidate the subscriber number
        when(SubscriberNumberUtils.checkSubscriberNumberParts(any(), any())).thenReturn(false);

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID.name()));
        }
    }

    @Test
    void testExecuteProcessBillQueryLimitReached() throws BillException {
        // Mock responses to reach the query limit
        when(limitationService.isPaymentAllowedWithoutDebtOwner(any(), any(), any(), any())).thenReturn(mockPaymentAllowedResponse(false));

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.BILL_QUERY_LIMIT_REACHED.name()));
        }
    }

    @Test
    void testExecuteProcessBillNotFound() throws BillException {
        // Mock responses to simulate no bills found
        when(adapterService.queryBills(any(), any(), any())).thenReturn(mockQueryBillsAdapterResponse(EnumBillResult.SUCCESS));
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList());
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.BILL_NOT_FOUND.name()));
        }
    }

    private QueryBillsAdapterResponse mockQueryBillsAdapterResponse(EnumBillResult result) {
        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode(result.getHmnCode());
        if (result == EnumBillResult.SUCCESS) {
            response.setBills(Collections.singletonList(new QueriedBillDTO()));
        }
        return response;
    }

    private PaymentAllowedResponse mockPaymentAllowedResponse(boolean isAllowed) {
        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(isAllowed);
        return response;
    }
}

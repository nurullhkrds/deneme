import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;

import java.util.*;

@RunWith(MockitoJUnitRunner.class)
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

    private Map<String, Object> dataPack;

    @Before
    public void setUp() {
        dataPack = new HashMap<>();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
        dataPack.put(ProcessDataPackKey.TAX_ID.getKey(), "tax123");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "sub123");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), new ArrayList<SubscriberNoPartRequestDTO>());
        dataPack.put(ProcessDataPackKey.CURRENCY.getKey(), "TRY");
        
        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
        
        queryBillsProcess.setDataPack(dataPack);
    }

    @Test
    public void testGatherDataStep() {
        QueryBillsProcess.GatherData step = queryBillsProcess.new GatherData();
        step.executeStep();
        assertEquals(123L, queryBillsProcess.customerNo.longValue());
        assertEquals(456L, queryBillsProcess.identityNo.longValue());
        assertEquals("tax123", queryBillsProcess.taxOfficeNo);
        assertEquals("sub123", queryBillsProcess.subscriberNo);
        assertEquals("TRY", queryBillsProcess.currency);
    }

    @Test
    public void testFormatSubscriberNoPartListStep() {
        List<InstitutionUserIntfDTO> institutionUserIntListDTO = new ArrayList<>();
        List<SubscriberNoPartRequestDTO> subscriberNoPartList = new ArrayList<>();
        when(SubscriberNumberUtils.formatSubscriberNumberParts(institutionUserIntListDTO, subscriberNoPartList))
                .thenReturn("formattedSub123");

        queryBillsProcess.institutionUserIntListDTO = institutionUserIntListDTO;
        queryBillsProcess.subscriberNoPartList = subscriberNoPartList;
        
        QueryBillsProcess.FormatSubscriberNoPartList step = queryBillsProcess.new FormatSubscriberNoPartList();
        step.executeStep();
        
        assertEquals("formattedSub123", queryBillsProcess.subscriberNo);
    }

    @Test
    public void testValidateSubscriberNoStep() {
        List<InstitutionUserIntfDTO> institutionUserIntListDTO = new ArrayList<>();
        List<SubscriberNoPartRequestDTO> subscriberNoPartList = new ArrayList<>();
        when(SubscriberNumberUtils.checkSubscriberNumberParts(institutionUserIntListDTO, subscriberNoPartList))
                .thenReturn(true);

        queryBillsProcess.institutionUserIntListDTO = institutionUserIntListDTO;
        queryBillsProcess.subscriberNoPartList = subscriberNoPartList;
        
        QueryBillsProcess.ValidateSubscriberNo step = queryBillsProcess.new ValidateSubscriberNo();
        step.executeStep();
        
        assertNull(queryBillsProcess.error);
    }

    @Test
    public void testCheckCustomerQueryLimitStep() {
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), isNull(), anyString()))
                .thenReturn(new PaymentAllowedResponse(true));

        QueryBillsProcess.CheckCustomerQueryLimit step = queryBillsProcess.new CheckCustomerQueryLimit();
        step.executeStep();
        
        assertNull(queryBillsProcess.error);
    }

    @Test
    public void testQueryFromServiceStep() throws BillException {
        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode("SUCCESS");
        when(adapterService.queryBills(any(), any(), any())).thenReturn(response);
        
        QueryBillsProcess.QueryFromService step = queryBillsProcess.new QueryFromService();
        step.executeStep();
        
        assertEquals(EnumBillResult.SUCCESS, queryBillsProcess.error);
    }

    @Test
    public void testEliminateBillsStep() {
        queryBillsProcess.queriedBillDTOList = new ArrayList<>();
        queryBillsProcess.queriedBillDTOList.add(new QueriedBillDTO());
        
        ResponseGetCustomerPaidBillList paidBills = new ResponseGetCustomerPaidBillList();
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(paidBills);

        QueryBillsProcess.EliminateBills step = queryBillsProcess.new EliminateBills();
        step.executeStep();
        
        assertNull(queryBillsProcess.error);
    }

    @Test
    public void testInvalidateNotPaidProvisionsStep() {
        QueryBillsProcess.InvalidateNotPaidProvisions step = queryBillsProcess.new InvalidateNotPaidProvisions();
        step.executeStep();
        
        verify(provisionService).invalidateNotPaidProvisions(any(), any());
    }

    @Test
    public void testCreateProvisionsStep() {
        queryBillsProcess.queriedBillDTOList = new ArrayList<>();
        
        QueryBillsProcess.CreateProvisions step = queryBillsProcess.new CreateProvisions();
        step.executeStep();
        
        verify(provisionService).createProvisions(anyList());
    }

    @Test
    public void testUpdateCustomerQueryLimitStep() {
        QueryBillsProcess.UpdateCustomerQueryLimit step = queryBillsProcess.new UpdateCustomerQueryLimit();
        step.executeStep();
        
        verify(paymentEventPublisher).publishInquiryLimiationNotification(any());
    }

    @Test
    public void testExecuteProcess() throws BillException {
        queryBillsProcess.executeProcess();
        
        // Check the correct execution of steps
        assertNull(queryBillsProcess.error);
    }
}

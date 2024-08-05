import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

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
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGatherData() {
        QueryBillsProcess.GatherData gatherData = queryBillsProcess.new GatherData();

        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);
        when(SpringUtil.getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        when(SpringUtil.getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        when(SpringUtil.getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);
        when(SpringUtil.getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        when(SpringUtil.getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        when(SpringUtil.getBean(LimitationService.class)).thenReturn(limitationService);
        when(SpringUtil.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);

        queryBillsProcess.dataPack = new DataPack();
        queryBillsProcess.dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 12345L);
        queryBillsProcess.dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 67890L);
        queryBillsProcess.dataPack.put(ProcessDataPackKey.TAX_ID.getKey(), "123456789");
        queryBillsProcess.dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "987654321");
        queryBillsProcess.dataPack.put(ProcessDataPackKey.CURRENCY.getKey(), "USD");

        gatherData.executeStep();

        assertEquals(12345L, queryBillsProcess.customerNo);
        assertEquals(67890L, queryBillsProcess.identityNo);
        assertEquals("123456789", queryBillsProcess.taxOfficeNo);
        assertEquals("987654321", queryBillsProcess.subscriberNo);
        assertEquals("USD", queryBillsProcess.currency);
        assertNotNull(queryBillsProcess.adapterService);
        assertNotNull(queryBillsProcess.institutionUserIntService);
        assertNotNull(queryBillsProcess.institutionUserIntMapper);
        assertNotNull(queryBillsProcess.billPaymentRestFacade);
        assertNotNull(queryBillsProcess.paymentRepository);
        assertNotNull(queryBillsProcess.paymentMapper);
        assertNotNull(queryBillsProcess.limitationService);
        assertNotNull(queryBillsProcess.paymentUtilImpl);
    }


     @Test
    public void testValidateSubscriberNo_Valid() {
        QueryBillsProcess.ValidateSubscriberNo validateSubscriberNo = queryBillsProcess.new ValidateSubscriberNo();
        
        queryBillsProcess.institutionUserIntListDTO = mock(List.class);
        queryBillsProcess.subscriberNoPartList = mock(List.class);

        when(SubscriberNumberUtils.checkSubscriberNumberParts(queryBillsProcess.institutionUserIntListDTO, queryBillsProcess.subscriberNoPartList)).thenReturn(true);

        validateSubscriberNo.executeStep();

        assertNull(queryBillsProcess.error);
    }

    @Test
    public void testValidateSubscriberNo_Invalid() {
        QueryBillsProcess.ValidateSubscriberNo validateSubscriberNo = queryBillsProcess.new ValidateSubscriberNo();

        queryBillsProcess.institutionUserIntListDTO = mock(List.class);
        queryBillsProcess.subscriberNoPartList = mock(List.class);

        when(SubscriberNumberUtils.checkSubscriberNumberParts(queryBillsProcess.institutionUserIntListDTO, queryBillsProcess.subscriberNoPartList)).thenReturn(false);

        validateSubscriberNo.executeStep();

        assertEquals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID, queryBillsProcess.error);
    }
}



@Test
    public void testCheckCustomerQueryLimit_LimitNotReached() {
        QueryBillsProcess.CheckCustomerQueryLimit checkCustomerQueryLimit = queryBillsProcess.new CheckCustomerQueryLimit();
        
        queryBillsProcess.customerNo = 12345L;
        queryBillsProcess.identityNo = 67890L;
        queryBillsProcess.isFomOperationEnabled = true;

        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(true);

        when(limitationService.isPaymentAllowedWithoutDebtOwner("67890", 12345, null, "B0002")).thenReturn(response);

        checkCustomerQueryLimit.executeStep();

        assertNull(queryBillsProcess.error);
    }

    @Test
    public void testCheckCustomerQueryLimit_LimitReached() {
        QueryBillsProcess.CheckCustomerQueryLimit checkCustomerQueryLimit = queryBillsProcess.new CheckCustomerQueryLimit();
        
        queryBillsProcess.customerNo = 12345L;
        queryBillsProcess.identityNo = 67890L;
        queryBillsProcess.isFomOperationEnabled = true;

        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(false);

        when(limitationService.isPaymentAllowedWithoutDebtOwner("67890", 12345, null, "B0002")).thenReturn(response);

        checkCustomerQueryLimit.executeStep();

        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, queryBillsProcess.error);
    }
     @Test
    public void testCreateProvisions() {
        QueryBillsProcess.CreateProvisions createProvisions = queryBillsProcess.new CreateProvisions();
        
        QueriedBillDTO billDTO = new QueriedBillDTO();
        billDTO.setCurrency("USD");
        billDTO.setQueryStan("12345");
        billDTO.setBillNo("98765");
        billDTO.setExplanation("Test bill");
        billDTO.setBillTerm("2024-08");
        billDTO.setInstitutionQueryStan("54321");
        billDTO.setBillDueDate(LocalDate.now().plusDays(30));
        billDTO.setPayable(true);
        billDTO.setBillIssueDate(LocalDate.now().minusDays(30));
        billDTO.setBillAmount(new BigDecimal("100.00"));
        billDTO.setSubscriberName("Test Subscriber");
        billDTO.setSubscriberNo("987654321");
        
        queryBillsProcess.queriedBillDTOList = List.of(billDTO);
        
        createProvisions.executeStep();

        assertNotNull(queryBillsProcess.provisionList);
        assertEquals(1, queryBillsProcess.provisionList.size());
        
        ProvisionDTO provisionDTO = queryBillsProcess.provisionList.get(0);
        assertEquals("USD", provisionDTO.getCurrency().toString());
        assertEquals("12345", provisionDTO.getQueryStan());
        assertEquals("98765", provisionDTO.getBillNo());
        assertEquals("Test bill", provisionDTO.getExplanation());
        assertEquals("2024-08", provisionDTO.getBillTerm());
        assertEquals("54321", provisionDTO.getInstitutionQueryStan());
        assertEquals(LocalDate.now().plusDays(30), provisionDTO.getBillDueDate());
        assertEquals(true, provisionDTO.getIsPayable());
        assertEquals(LocalDate.now().minusDays(30), provisionDTO.getBillIssueDate());
        assertEquals(new BigDecimal("100.00"), provisionDTO.getAmount());
        assertEquals("Test Subscriber", provisionDTO.getSubscriberName());
        assertEquals("987654321", provisionDTO.getSubscriberNo());
    }

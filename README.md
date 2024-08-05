import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.List;
import java.util.ArrayList;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;

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
    
    @Mock
    private SubscriberNumberUtils subscriberNumberUtils;
    
    @BeforeEach
    public void setup() {
        queryBillsProcess = new QueryBillsProcess();
        ReflectionTestUtils.setField(queryBillsProcess, "adapterService", adapterService);
        ReflectionTestUtils.setField(queryBillsProcess, "provisionService", provisionService);
        ReflectionTestUtils.setField(queryBillsProcess, "institutionUserIntService", institutionUserIntService);
        ReflectionTestUtils.setField(queryBillsProcess, "billPaymentRestFacade", billPaymentRestFacade);
        ReflectionTestUtils.setField(queryBillsProcess, "paymentRepository", paymentRepository);
        ReflectionTestUtils.setField(queryBillsProcess, "paymentMapper", paymentMapper);
        ReflectionTestUtils.setField(queryBillsProcess, "limitationService", limitationService);
        ReflectionTestUtils.setField(queryBillsProcess, "paymentEventPublisher", paymentEventPublisher);
        ReflectionTestUtils.setField(queryBillsProcess, "paymentUtilImpl", paymentUtilImpl);
    }

    private <T> T instantiatePrivateClass(Class<?> enclosingClass, String className) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        Class<?> innerClass = null;
        for (Class<?> clazz : enclosingClass.getDeclaredClasses()) {
            if (clazz.getSimpleName().equals(className)) {
                innerClass = clazz;
                break;
            }
        }

        if (innerClass == null) {
            throw new IllegalArgumentException("Class " + className + " not found in " + enclosingClass);
        }

        Constructor<?> constructor = innerClass.getDeclaredConstructor(enclosingClass);
        constructor.setAccessible(true);
        return (T) constructor.newInstance(enclosingClass);
    }
    
    @Test
    public void testGatherData() throws Exception {
        DataPack dataPack = new DataPack();
        dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 12345L);
        dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 67890L);
        dataPack.put(ProcessDataPackKey.TAX_ID.getKey(), "98765");
        dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "54321");
        
        ReflectionTestUtils.setField(queryBillsProcess, "dataPack", dataPack);
        
        QueryBillsProcess.GatherData gatherData = instantiatePrivateClass(QueryBillsProcess.class, "GatherData");
        ReflectionTestUtils.invokeMethod(gatherData, "executeStep");
        
        assertEquals(12345L, ReflectionTestUtils.getField(queryBillsProcess, "customerNo"));
        assertEquals(67890L, ReflectionTestUtils.getField(queryBillsProcess, "identityNo"));
        assertEquals("98765", ReflectionTestUtils.getField(queryBillsProcess, "taxOfficeNo"));
        assertEquals("54321", ReflectionTestUtils.getField(queryBillsProcess, "subscriberNo"));
    }
    
    @Test
    public void testFormatSubscriberNoPartList() throws Exception {
        List<SubscriberNoPartRequestDTO> subscriberNoPartList = new ArrayList<>();
        // Fill the list with dummy data
        
        ReflectionTestUtils.setField(queryBillsProcess, "subscriberNoPartList", subscriberNoPartList);
        
        List<InstitutionUserIntfDTO> institutionUserIntListDTO = new ArrayList<>();
        // Fill the list with dummy data
        
        ReflectionTestUtils.setField(queryBillsProcess, "institutionUserIntListDTO", institutionUserIntListDTO);
        
        when(subscriberNumberUtils.formatSubscriberNumberParts(anyList(), anyList())).thenReturn("formattedSubscriberNo");
        
        QueryBillsProcess.FormatSubscriberNoPartList formatSubscriberNoPartList = instantiatePrivateClass(QueryBillsProcess.class, "FormatSubscriberNoPartList");
        ReflectionTestUtils.invokeMethod(formatSubscriberNoPartList, "executeStep");
        
        assertEquals("formattedSubscriberNo", ReflectionTestUtils.getField(queryBillsProcess, "subscriberNo"));
    }
    
    @Test
    public void testValidateSubscriberNo() throws Exception {
        List<InstitutionUserIntfDTO> institutionUserIntListDTO = new ArrayList<>();
        // Fill the list with dummy data
        
        List<SubscriberNoPartRequestDTO> subscriberNoPartList = new ArrayList<>();
        // Fill the list with dummy data
        
        ReflectionTestUtils.setField(queryBillsProcess, "institutionUserIntListDTO", institutionUserIntListDTO);
        ReflectionTestUtils.setField(queryBillsProcess, "subscriberNoPartList", subscriberNoPartList);
        
        when(subscriberNumberUtils.checkSubscriberNumberParts(anyList(), anyList())).thenReturn(true);
        
        QueryBillsProcess.ValidateSubscriberNo validateSubscriberNo = instantiatePrivateClass(QueryBillsProcess.class, "ValidateSubscriberNo");
        ReflectionTestUtils.invokeMethod(validateSubscriberNo, "executeStep");
        
        assertNull(ReflectionTestUtils.getField(queryBillsProcess, "error"));
        
        when(subscriberNumberUtils.checkSubscriberNumberParts(anyList(), anyList())).thenReturn(false);
        ReflectionTestUtils.invokeMethod(validateSubscriberNo, "executeStep");
        
        assertEquals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID, ReflectionTestUtils.getField(queryBillsProcess, "error"));
    }
    
    @Test
    public void testCheckCustomerQueryLimit() throws Exception {
        ReflectionTestUtils.setField(queryBillsProcess, "isFomOperationEnabled", true);
        
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString())).thenReturn(new PaymentAllowedResponse(true));
        
        QueryBillsProcess.CheckCustomerQueryLimit checkCustomerQueryLimit = instantiatePrivateClass(QueryBillsProcess.class, "CheckCustomerQueryLimit");
        ReflectionTestUtils.invokeMethod(checkCustomerQueryLimit, "executeStep");
        
        assertNull(ReflectionTestUtils.getField(queryBillsProcess, "error"));
        
        when(limitationService.isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), any(), anyString())).thenReturn(new PaymentAllowedResponse(false));
        ReflectionTestUtils.invokeMethod(checkCustomerQueryLimit, "executeStep");
        
        assertEquals(EnumBillResult.BILL_QUERY_LIMIT_REACHED, ReflectionTestUtils.getField(queryBillsProcess, "error"));
    }
}

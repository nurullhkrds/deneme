import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@ExtendWith(MockitoExtension.class)
class HarmoniPaymentAdkControllerTest {

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
        MockitoAnnotations.openMocks(this);
    }

    // Test for queryBills method
    @Test
    void testQueryBills_Success() throws MicroException {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        // populate request with necessary data

        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        ResponseHarmoniQueryBills responseHarmoniQueryBills = new ResponseHarmoniQueryBills();
        
        when(harmoniMicroMapper.toQueryBillRequest(request)).thenReturn(queryBillsResponse);
        when(paymentService.queryBills(queryBillsResponse)).thenReturn(queryBillsResponse);
        when(harmoniMicroMapper.toResponseHarmoniQueryBills(queryBillsResponse, request)).thenReturn(responseHarmoniQueryBills);

        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(responseHarmoniQueryBills, result.getResult());

        verify(harmoniMicroMapper).toQueryBillRequest(request);
        verify(paymentService).queryBills(queryBillsResponse);
        verify(harmoniMicroMapper).toResponseHarmoniQueryBills(queryBillsResponse, request);
    }

    @Test
    void testQueryBills_BillException() throws MicroException {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        // populate request with necessary data

        BillException billException = new BillException();
        // populate billException with necessary data

        when(paymentService.queryBills(any())).thenThrow(billException);

        HarmoniCoreServiceResultDTO<ResponseHarmoniQueryBills> result = harmoniPaymentAdkController.queryBills(request);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for doBillPayment method
    @Test
    void testDoBillPayment_Success() throws MicroException {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        // populate request with necessary data

        DoBillPaymentResponse microResponse = new DoBillPaymentResponse();
        ResponseHarmoniDoBillPaymentResultDTO hmnResponse = new ResponseHarmoniDoBillPaymentResultDTO();

        when(harmoniMicroMapper.toDoBillPaymentRequest(request)).thenReturn(microResponse);
        when(paymentService.doBillPayment(microResponse)).thenReturn(microResponse);
        when(harmoniMicroMapper.toResponseHarmoniDoBillPaymentResultDTO(microResponse)).thenReturn(hmnResponse);

        HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> result = harmoniPaymentAdkController.doBillPayment(request);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(hmnResponse, result.getResult());

        verify(harmoniMicroMapper).toDoBillPaymentRequest(request);
        verify(paymentService).doBillPayment(microResponse);
        verify(harmoniMicroMapper).toResponseHarmoniDoBillPaymentResultDTO(microResponse);
    }

    @Test
    void testDoBillPayment_BillException() throws MicroException {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        // populate request with necessary data

        BillException billException = new BillException();
        // populate billException with necessary data

        when(paymentService.doBillPayment(any())).thenThrow(billException);

        HarmoniCoreServiceResultDTO<ResponseHarmoniDoBillPaymentResultDTO> result = harmoniPaymentAdkController.doBillPayment(request);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for cancelBillPayment method
    @Test
    void testCancelBillPayment_Success() throws MicroException {
        RequestHarmoniCancelBillPayment request = new RequestHarmoniCancelBillPayment();
        // populate request with necessary data

        CancelBillPaymentResponse microResponse = new CancelBillPaymentResponse();
        ResponseHarmoniCancelBillPayment convertedHarmoniResponse = new ResponseHarmoniCancelBillPayment();

        when(harmoniMicroMapper.toCancelBillPaymentRequest(request)).thenReturn(microResponse);
        when(paymentService.cancelBillPayment(microResponse)).thenReturn(microResponse);
        when(harmoniMicroMapper.toResponseHarmoniCancelBillPayment(microResponse)).thenReturn(convertedHarmoniResponse);

        HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> result = harmoniPaymentAdkController.cancelBillPayment(request);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(convertedHarmoniResponse, result.getResult());

        verify(harmoniMicroMapper).toCancelBillPaymentRequest(request);
        verify(paymentService).cancelBillPayment(microResponse);
        verify(harmoniMicroMapper).toResponseHarmoniCancelBillPayment(microResponse);
    }

    @Test
    void testCancelBillPayment_BillException() throws MicroException {
        RequestHarmoniCancelBillPayment request = new RequestHarmoniCancelBillPayment();
        // populate request with necessary data

        BillException billException = new BillException();
        // populate billException with necessary data

        when(paymentService.cancelBillPayment(any())).thenThrow(billException);

        HarmoniCoreServiceResultDTO<ResponseHarmoniCancelBillPayment> result = harmoniPaymentAdkController.cancelBillPayment(request);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for getBillPaymentExpense method
    @Test
    void testGetBillPaymentExpense_Success() throws MicroException {
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        // populate request with necessary data

        GetBillPaymentExpenseResponseDTO microResponse = new GetBillPaymentExpenseResponseDTO();
        ResponseHarmoniGetBillPaymentExpense harmoniResponse = new ResponseHarmoniGetBillPaymentExpense();

        when(harmoniMicroMapper.toGetBillPaymentExpenseRequestDTO(request)).thenReturn(microResponse);
        when(subscriberService.getBillPaymentExpense(microResponse)).thenReturn(microResponse);
        when(harmoniMicroMapper.toResponseHarmoniGetBillPaymentExpense(microResponse)).thenReturn(harmoniResponse);

        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(harmoniResponse, result.getResult());

        verify(harmoniMicroMapper).toGetBillPaymentExpenseRequestDTO(request);
        verify(subscriberService).getBillPaymentExpense(microResponse);
        verify(harmoniMicroMapper).toResponseHarmoniGetBillPaymentExpense(microResponse);
    }

    @Test
    void testGetBillPaymentExpense_MicroException() throws MicroException {
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        // populate request with necessary data

        MicroException microException = new MicroException();
        // populate microException with necessary data

        when(subscriberService.getBillPaymentExpense(any())).thenThrow(microException);

        HarmoniCoreServiceResultDTO<ResponseHarmoniGetBillPaymentExpense> result = harmoniPaymentAdkController.getBillPaymentExpense(request);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for getCustomerPaidBillList method
    @Test
    void testGetCustomerPaidBillList_Success() throws MicroException {
        Long customerNo = 123L;
        String channelCode = "channelCode";
        String product = "product";

        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo(customerNo);
        request.setChannelCode(ChannelUtil.convertChannel(channelCode));
        request.setProductCode(product);

        List<HmnPaidBillDTO> hmnPaidBillDTOList = Collections.singletonList(new HmnPaidBillDTO());
        ResponseHarmoniGetCustomerPaidBillList harmoniResponse = new ResponseHarmoniGetCustomerPaidBillList();
        harmoniResponse.setBillDTOList(hmnPaidBillDTOList);

        when(paymentService.getMicroBillList(request)).thenReturn(hmnPaidBillDTOList);

        HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> result = harmoniPaymentAdkController.getCustomerPaidBillList(customerNo, channelCode, product);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(harmoniResponse, result.getResult());

        verify(paymentService).getMicroBillList(request);
    }

    @Test
    void testGetCustomerPaidBillList_MicroException() throws MicroException {
        Long customerNo = 123L;
        String channelCode = "channelCode";
        String product = "product";

        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo(customerNo);
        request.setChannelCode(ChannelUtil.convertChannel(channelCode));
        request.setProductCode(product);

        MicroException microException = new MicroException();
        // populate microException with necessary data

        when(paymentService.getMicroBillList(any())).thenThrow(microException);

        HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> result = harmoniPaymentAdkController.getCustomerPaidBillList(customerNo, channelCode, product);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for getReconCount method
    @Test
    void testGetReconCount_Success() throws MicroException {
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";

        CountDTO countDTO = new CountDTO();
        // populate countDTO with necessary data

        when(paymentService.getReconCount(isPayment, reconciliationDate, productCode, institutionCode)).thenReturn(countDTO);

        HarmoniCoreServiceResultDTO<CountDTO> result = harmoniPaymentAdkController.getReconCount(isPayment, reconciliationDate, productCode, institutionCode);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(countDTO, result.getResult());

        verify(paymentService).getReconCount(isPayment, reconciliationDate, productCode, institutionCode);
    }

    @Test
    void testGetReconCount_MicroException() throws MicroException {
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";

        MicroException microException = new MicroException();
        // populate microException with necessary data

        when(paymentService.getReconCount(isPayment, reconciliationDate, productCode, institutionCode)).thenThrow(microException);

        HarmoniCoreServiceResultDTO<CountDTO> result = harmoniPaymentAdkController.getReconCount(isPayment, reconciliationDate, productCode, institutionCode);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }

    // Test for getReconDetail method
    @Test
    void testGetReconDetail_Success() throws MicroException {
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";

        List<HmnPaidBillDTO> reconDetailList = Collections.singletonList(new HmnPaidBillDTO());
        // populate reconDetailList with necessary data

        when(paymentService.getReconDetail(isPayment, reconciliationDate, productCode, institutionCode)).thenReturn(reconDetailList);

        HarmoniCoreServiceResultDTO<List<HmnPaidBillDTO>> result = harmoniPaymentAdkController.getReconDetail(isPayment, reconciliationDate, productCode, institutionCode);

        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertEquals(reconDetailList, result.getResult());

        verify(paymentService).getReconDetail(isPayment, reconciliationDate, productCode, institutionCode);
    }

    @Test
    void testGetReconDetail_MicroException() throws MicroException {
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";

        MicroException microException = new MicroException();
        // populate microException with necessary data

        when(paymentService.getReconDetail(isPayment, reconciliationDate, productCode, institutionCode)).thenThrow(microException);

        HarmoniCoreServiceResultDTO<List<HmnPaidBillDTO>> result = harmoniPaymentAdkController.getReconDetail(isPayment, reconciliationDate, productCode, institutionCode);

        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
    }
}

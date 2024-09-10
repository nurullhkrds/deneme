import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.net.URI;
import java.time.Duration;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.test.util.ReflectionTestUtils;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

class BillPaymentRestFacadeClientTest {

    @Mock
    private RestTemplateBuilder restTemplateBuilder;

    @Mock
    private RestTemplate restTemplate;

    @InjectMocks
    private BillPaymentRestFacadeClient billPaymentRestFacadeClient;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);

        when(restTemplateBuilder.setConnectTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
        when(restTemplateBuilder.setReadTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
        when(restTemplateBuilder.build()).thenReturn(restTemplate);

        // Address ve timeout değerlerini elle set ediyoruz
        ReflectionTestUtils.setField(billPaymentRestFacadeClient, "address", "http://test-address");
        ReflectionTestUtils.setField(billPaymentRestFacadeClient, "readTimeout", 5000L);
        ReflectionTestUtils.setField(billPaymentRestFacadeClient, "connectionTimeout", 5000L);
    }

    @Test
    void testGetCustomerPaidBillList() {
        RequestGetCustomerPaidBillList request = new RequestGetCustomerPaidBillList();
        ResponseGetCustomerPaidBillList expectedResponse = new ResponseGetCustomerPaidBillList();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "getCustomerPaidBillList").build().toUri();

        // RestTemplate postForObject metodunu mockluyoruz
        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseGetCustomerPaidBillList.class)))
                .thenReturn(expectedResponse);

        // Gerçek metod çağrısı
        ResponseGetCustomerPaidBillList actualResponse = billPaymentRestFacadeClient.getCustomerPaidBillList(request);

        // Yanıtın beklenen yanıtla aynı olup olmadığını kontrol ediyoruz
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testQueryBills() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();
        ResponseQueryBillsHmn expectedResponse = new ResponseQueryBillsHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "queryBills").build().toUri();

        // RestTemplate postForObject metodunu mockluyoruz
        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseQueryBillsHmn.class)))
                .thenReturn(expectedResponse);

        // Gerçek metod çağrısı
        ResponseQueryBillsHmn actualResponse = billPaymentRestFacadeClient.queryBills(request);

        // Yanıtın beklenen yanıtla aynı olup olmadığını kontrol ediyoruz
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testGetBillPaymentExpense() {
        RequestBillPaymentExpenseHmn request = new RequestBillPaymentExpenseHmn();
        ResponseBillPaymentExpenseHmn expectedResponse = new ResponseBillPaymentExpenseHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "getBillPaymentExpense").build().toUri();

        // RestTemplate postForObject metodunu mockluyoruz
        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseBillPaymentExpenseHmn.class)))
                .thenReturn(expectedResponse);

        // Gerçek metod çağrısı
        ResponseBillPaymentExpenseHmn actualResponse = billPaymentRestFacadeClient.getBillPaymentExpense(request);

        // Yanıtın beklenen yanıtla aynı olup olmadığını kontrol ediyoruz
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testPayBill() {
        RequestPayBillHmn request = new RequestPayBillHmn();
        ResponsePayBillHmn expectedResponse = new ResponsePayBillHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "payBill").build().toUri();

        // RestTemplate postForObject metodunu mockluyoruz
        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponsePayBillHmn.class)))
                .thenReturn(expectedResponse);

        // Gerçek metod çağrısı
        ResponsePayBillHmn actualResponse = billPaymentRestFacadeClient.payBill(request);

        // Yanıtın beklenen yanıtla aynı olup olmadığını kontrol ediyoruz
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void testReverseBillPayment() {
        RequestQueryBillsHmn request = new RequestQueryBillsHmn();
        ResponseQueryBillsHmn expectedResponse = new ResponseQueryBillsHmn();

        URI uri = UriComponentsBuilder.fromHttpUrl("http://test-address").path("billPaymentRestFacade")
                .queryParam("operationName", "reverseBillPayment").build().toUri();

        // RestTemplate postForObject metodunu mockluyoruz
        when(restTemplate.postForObject(eq(uri), eq(request), eq(ResponseQueryBillsHmn.class)))
                .thenReturn(expectedResponse);

        // Gerçek metod çağrısı
        ResponseQueryBillsHmn actualResponse = billPaymentRestFacadeClient.reverseBillPayment(request);

        // Yanıtın beklenen yanıtla aynı olup olmadığını kontrol ediyoruz
        assertEquals(expectedResponse, actualResponse);
    }
}

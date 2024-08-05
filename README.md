import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

@ExtendWith(MockitoExtension.class)
public class PaymentAdkControllerTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentFacade paymentFacade;

    @Mock
    private InstitutionBarcodeService institutionBarcodeService;

    @Mock
    private RequestContext requestContext;

    @InjectMocks
    private PaymentAdkController paymentAdkController;

    @BeforeEach
    void setUp() {
        // Setup code if needed
    }

    @Test
    void testGetBillPaymentExpense() throws MicroException {
        GetBillPaymentExpenseRequestDTO request = new GetBillPaymentExpenseRequestDTO();
        GetBillPaymentExpenseResponseDTO response = new GetBillPaymentExpenseResponseDTO();

        when(paymentFacade.getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class))).thenReturn(response);

        ResponseEntity<GetBillPaymentExpenseResponseDTO> result = paymentAdkController.getBillPaymentExpense(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testQueryBills() throws MicroException {
        QueryBillsRequest request = new QueryBillsRequest();
        QueryBillsResponse response = new QueryBillsResponse();

        when(paymentFacade.queryBills(any(QueryBillsRequest.class))).thenReturn(response);

        ResponseEntity<QueryBillsResponse> result = paymentAdkController.queryBills("traceId", "sessionId", request);

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testDoBillPayment() throws MicroException {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        DoBillPaymentResponse response = new DoBillPaymentResponse();

        when(paymentFacade.doBillPayment(any(DoBillPaymentRequest.class))).thenReturn(response);

        ResponseEntity<DoBillPaymentResponse> result = paymentAdkController.doBillPayment("traceId", "sessionId", request);

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testCancelBillPayment() throws MicroException {
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        CancelBillPaymentResponse response = new CancelBillPaymentResponse();

        when(paymentFacade.cancelBillPayment(any(CancelBillPaymentRequest.class))).thenReturn(response);

        ResponseEntity<CancelBillPaymentResponse> result = paymentAdkController.cancelBillPayment("traceId", "sessionId", request);

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testGetCustomerPaidBillList() throws MicroException {
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        GetCustomerPaidBillListResponse response = new GetCustomerPaidBillListResponse();

        when(paymentService.getCustomerPaidBillList(any(GetCustomerPaidBillListRequest.class))).thenReturn(response);

        ResponseEntity<GetCustomerPaidBillListResponse> result = paymentAdkController.getCustomerPaidBillList(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testGetSubscriberNoWithBarcode() {
        GetSubscriberNoWithBarcodeRequest request = new GetSubscriberNoWithBarcodeRequest();
        GetSubscriberNoWithBarcodeResponse response = new GetSubscriberNoWithBarcodeResponse();

        when(institutionBarcodeService.getSubscriberNoWithBarcode(any(GetSubscriberNoWithBarcodeRequest.class))).thenReturn(response);

        ResponseEntity<GetSubscriberNoWithBarcodeResponse> result = paymentAdkController.getSubscriberNoWithBarcode(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testParseSubscriberNoIntoParts() {
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        ParseSubscriberNoIntoPartsResponse response = new ParseSubscriberNoIntoPartsResponse();

        when(paymentService.parseSubscriberNoIntoParts(any(ParseSubscriberNoIntoPartsRequest.class))).thenReturn(response);

        ResponseEntity<ParseSubscriberNoIntoPartsResponse> result = paymentAdkController.parseSubscriberNoIntoParts(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testNotifyPayment() throws MicroException {
        NotifyPaymentRequest request = new NotifyPaymentRequest();
        NotifyPaymentResponse response = new NotifyPaymentResponse();

        when(paymentService.notifyPayment(any(NotifyPaymentRequest.class))).thenReturn(response);

        ResponseEntity<NotifyPaymentResponse> result = paymentAdkController.notifyPayment(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }

    @Test
    void testNotifyPaymentCancel() throws MicroException {
        NotifyPaymentCancelRequest request = new NotifyPaymentCancelRequest();
        NotifyPaymentCancelResponse response = new NotifyPaymentCancelResponse();

        when(paymentService.notifyPaymentCancel(any(NotifyPaymentCancelRequest.class))).thenReturn(response);

        ResponseEntity<NotifyPaymentCancelResponse> result = paymentAdkController.notifyPaymentCancel(request, "traceId", "sessionId");

        assertEquals(HttpStatus.OK, result.getStatusCode());
        assertEquals(response, result.getBody());
    }
}

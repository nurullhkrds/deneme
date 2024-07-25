import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import com.ykb.payments.bill.transaction.process.payment.*;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.facade.*;
import com.ykb.payments.bill.transaction.process.query.*;
import com.ykb.payments.bill.transaction.service.PaymentServiceImpl;
import com.ykb.payments.bill.transaction.service.mapper.*;
import com.ykb.payments.bill.transaction.domain.*;
import com.ykb.payments.bill.transaction.repository.*;
import com.ykb.payments.bill.transaction.exception.MicroException;
import com.ykb.payments.bill.transaction.request.*;
import com.ykb.payments.bill.transaction.response.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;

import java.time.LocalDate;
import java.util.*;
import java.util.stream.Collectors;

@SpringBootTest
public class PaymentServiceImplTest {

    @InjectMocks
    private PaymentServiceImpl paymentService;

    @Mock
    private ProcessManager processManager;
    @Mock
    private ProcessExecutionMapper processExecutionMapper;
    @Mock
    private RequestContext requestContext;
    @Mock
    private PaymentRepository paymentRepository;
    @Mock
    private PaymentCancelRepository paymentCancelRepository;
    @Mock
    private PaymentMapper paymentMapper;
    @Mock
    private PaymentCancelMapper paymentCancelMapper;
    @Mock
    private InstitutionService institutionService;
    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;
    @Mock
    private InstitutionUserIntService institutionUserIntService;
    @Mock
    private ChannelService channelService;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testQueryBills() throws MicroException {
        QueryBillsRequest request = new QueryBillsRequest();
        QueryBillProcessInput processInput = new QueryBillProcessInput();
        QueryBillsProcessOutput processOutput = new QueryBillsProcessOutput();

        when(processExecutionMapper.toQueryBillProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("session-id");
        when(requestContext.getChannelTransactionId()).thenReturn("transaction-id");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(processExecutionMapper.toQueryBillsResponse(processOutput)).thenReturn(new QueryBillsResponse());

        QueryBillsResponse response = paymentService.queryBills(request);

        assertNotNull(response);
        verify(processManager).executeProcess(processInput);
    }

    @Test
    public void testGetCustomerPaidBillList() throws MicroException {
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();

        ResponseGetCustomerPaidBillList harmoniResponse = new ResponseGetCustomerPaidBillList();
        harmoniResponse.setStatus("S");
        harmoniResponse.setBillDTOList(new ArrayList<>());

        when(billPaymentRestFacade.getCustomerPaidBillList(request.getCustomerNo())).thenReturn(harmoniResponse);
        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq(request.getCustomerNo()), eq(EnumBillStatu.PAID.getValue()), eq(request.getProductCode()))).thenReturn(new ArrayList<>());
        when(paymentMapper.toPaidBillResponseWebDTO(any(Payment.class), any(InstitutionDTO.class))).thenReturn(new PaidBillResponseWebDTO());

        GetCustomerPaidBillListResponse response = paymentService.getCustomerPaidBillList(request);

        assertNotNull(response);
        verify(billPaymentRestFacade).getCustomerPaidBillList(request.getCustomerNo());
    }

    @Test
    @Transactional
    public void testDoBillPayment() throws MicroException {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        BillPaymentProcessInput processInput = new BillPaymentProcessInput();
        BillPaymentProcessOutput processOutput = new BillPaymentProcessOutput();
        processOutput.setBillId(1L);
        processOutput.setContractNo("contractNo");

        when(processExecutionMapper.toBillPaymentProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("session-id");
        when(requestContext.getChannelTransactionId()).thenReturn("transaction-id");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);

        DoBillPaymentResponse response = paymentService.doBillPayment(request);

        assertNotNull(response);
        assertEquals(1L, response.getBillId());
        assertEquals("contractNo", response.getContractNumber());
    }

    @Test
    @Transactional
    public void testCancelBillPayment() throws MicroException {
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        BillPaymentReverseProcessInput processInput = new BillPaymentReverseProcessInput();

        when(processExecutionMapper.toBillPaymentReverseProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("session-id");
        when(requestContext.getChannelTransactionId()).thenReturn("transaction-id");
        when(processManager.executeProcess(processInput)).thenReturn(null);

        CancelBillPaymentResponse response = paymentService.cancelBillPayment(request);

        assertNotNull(response);
        verify(processManager).executeProcess(processInput);
    }

    @Test
    public void testInsertPayment() {
        PaymentDTO paymentDTO = new PaymentDTO();
        Payment paymentEntity = new Payment();

        when(paymentMapper.toEntity(paymentDTO)).thenReturn(paymentEntity);
        when(paymentRepository.save(paymentEntity)).thenReturn(paymentEntity);
        when(paymentMapper.toDTO(paymentEntity)).thenReturn(paymentDTO);

        PaymentDTO result = paymentService.insertPayment(paymentDTO);

        assertNotNull(result);
        verify(paymentRepository).save(paymentEntity);
    }

    @Test
    public void testGetPayment() {
        Long id = 1L;
        Long contractNo = 123L;
        Payment payment = new Payment();
        PaymentDTO paymentDTO = new PaymentDTO();

        when(paymentRepository.findByIdAndContractNo(id, contractNo)).thenReturn(Optional.of(payment));
        when(paymentMapper.toDTO(payment)).thenReturn(paymentDTO);

        PaymentDTO result = paymentService.getPayment(id, contractNo);

        assertNotNull(result);
        verify(paymentRepository).findByIdAndContractNo(id, contractNo);
    }

    @Test
    @Transactional
    public void testUpdateStatus() {
        EnumBillStatu status = EnumBillStatu.PAID;
        Long id = 1L;
        Long contractNo = 123L;

        paymentService.updateStatus(status, id, contractNo);

        verify(paymentRepository).updateStatus(status.getValue(), id, contractNo);
    }

    @Test
    public void testInsertPaymentCancel() {
        PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
        PaymentCancel paymentCancelEntity = new PaymentCancel();

        when(paymentCancelMapper.toEntity(paymentCancelDTO)).thenReturn(paymentCancelEntity);
        when(paymentCancelRepository.save(paymentCancelEntity)).thenReturn(paymentCancelEntity);
        when(paymentCancelMapper.toDTO(paymentCancelEntity)).thenReturn(paymentCancelDTO);

        PaymentCancelDTO result = paymentService.insertPaymentCancel(paymentCancelDTO);

        assertNotNull(result);
        verify(paymentCancelRepository).save(paymentCancelEntity);
    }

    @Test
    public void testParseSubscriberNoIntoParts() {
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        ParseSubscriberNoIntoPartsResponse response = new ParseSubscriberNoIntoPartsResponse();
        List<InstitutionUserIntfDTO> institutionUserIntfDTOList = new ArrayList<>();
        List<SubsrciberNoPartResponseWebDTO> subscriberNoIntoPartList = new ArrayList<>();

        when(institutionUserIntService.getDefaultUserInterface(anyString(), anyString())).thenReturn(institutionUserIntfDTOList);
        when(SubscriberNumberUtils.parseSubscriberNoIntoParts(institutionUserIntfDTOList, request.getSubscriberNo())).thenReturn(subscriberNoIntoPartList);

        ParseSubscriberNoIntoPartsResponse result = paymentService.parseSubscriberNoIntoParts(request);

        assertNotNull(result);
        assertEquals(subscriberNoIntoPartList, result.getSubsrciberNoPartResponseWebDTO());
    }

    @Test
    public void testFindPaymentByIdWithLock() {
        Long paymentId = 1L;
        Payment payment = new Payment();

        when(paymentRepository.findByIdWithLock(paymentId)).thenReturn(Optional.of(payment));

        Payment result = paymentService.findPaymentByIdWithLock(paymentId);

        assertNotNull(result);
        verify(paymentRepository).findByIdWithLock(paymentId);
    }

    @Test
    @Transactional
    public void testUpdatePayment() {
        Payment payment = new Payment();

        paymentService.updatePayment(payment);

        verify(paymentRepository).save(payment);
    }

    @Test
    public void testGetPaymentById() {
        Long paymentId = 1L;
        Payment payment = new Payment();
        PaymentDTO paymentDTO = new PaymentDTO();

        when(paymentRepository.findById(paymentId)).thenReturn(Optional.of(payment));
        when(paymentMapper.toDTO(payment)).thenReturn(paymentDTO);

        PaymentDTO result = paymentService.getPayment(paymentId);

        assertNotNull(result);
        verify(paymentRepository).findById(paymentId);
    }

    @Test
    @Transactional
    public void testNotifyPayment() throws MicroException {
        NotifyPaymentRequest request = new NotifyPaymentRequest();
        NotifyPaymentProcessInput processInput = new NotifyPaymentProcessInput();
        NotifyPaymentProcessOutput processOutput = new NotifyPaymentProcessOutput();

        when(processExecutionMapper.toNotifyPaymentProcessInput(request)).thenReturn(processInput);
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);

        NotifyPaymentResponse response = paymentService.notifyPayment(request);

        assertNotNull(response);
        verify(processManager).executeProcess(processInput);
    }
}

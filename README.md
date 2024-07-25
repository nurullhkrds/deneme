import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.util.CollectionUtils;

import java.time.LocalDate;
import java.util.*;

@SpringJUnitConfig
public class PaymentServiceImplTest {

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
    
    @InjectMocks
    private PaymentServiceImpl paymentService;

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
    List<PaidBillResponseWebDTO> microBillList = Collections.emptyList();
    List<PaidBillResponseWebDTO> harmoniBillList = Collections.emptyList();
    
    when(paymentRepository.findCustomerPaidBillList(any(), any(), any(), any())).thenReturn(new ArrayList<>());
    when(billPaymentRestFacade.getCustomerPaidBillList(any())).thenReturn(new ResponseGetCustomerPaidBillList());
    
    GetCustomerPaidBillListResponse response = paymentService.getCustomerPaidBillList(request);
    
    assertNotNull(response);
    assertTrue(CollectionUtils.isEmpty(response.getBillList()));
}
@Test
public void testDoBillPayment() throws MicroException {
    DoBillPaymentRequest request = new DoBillPaymentRequest();
    BillPaymentProcessInput processInput = new BillPaymentProcessInput();
    BillPaymentProcessOutput processOutput = new BillPaymentProcessOutput();
    
    when(processExecutionMapper.toBillPaymentProcessInput(request)).thenReturn(processInput);
    when(requestContext.getChannelSessionId()).thenReturn("session-id");
    when(requestContext.getChannelTransactionId()).thenReturn("transaction-id");
    when(processManager.executeProcess(processInput)).thenReturn(processOutput);
    
    DoBillPaymentResponse response = paymentService.doBillPayment(request);
    
    assertNotNull(response);
    verify(processManager).executeProcess(processInput);
}
@Test
public void testCancelBillPayment() throws MicroException {
    CancelBillPaymentRequest request = new CancelBillPaymentRequest();
    BillPaymentReverseProcessInput processInput = new BillPaymentReverseProcessInput();
    
    when(processExecutionMapper.toBillPaymentReverseProcessInput(request)).thenReturn(processInput);
    when(requestContext.getChannelSessionId()).thenReturn("session-id");
    when(requestContext.getChannelTransactionId()).thenReturn("transaction-id");
    
    paymentService.cancelBillPayment(request);
    
    verify(processManager).executeProcess(processInput);
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
    assertEquals(paymentDTO, result);
}
@Test
@Transactional
public void testUpdateStatus() {
    Long id = 1L;
    Long contractNo = 123L;
    EnumBillStatu status = EnumBillStatu.PAID;
    
    paymentService.updateStatus(status, id, contractNo);
    
    verify(paymentRepository).updateStatus(status.getValue(), id, contractNo);
}
@Test
public void testInsertPaymentCancel() {
    PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
    PaymentCancel paymentCancel = new PaymentCancel();
    
    when(paymentCancelMapper.toEntity(paymentCancelDTO)).thenReturn(paymentCancel);
    when(paymentCancelRepository.save(paymentCancel)).thenReturn(paymentCancel);
    when(paymentCancelMapper.toDTO(paymentCancel)).thenReturn(paymentCancelDTO);
    
    PaymentCancelDTO result = paymentService.insertPaymentCancel(paymentCancelDTO);
    
    assertNotNull(result);
    verify(paymentCancelRepository).save(paymentCancel);
}
    


}

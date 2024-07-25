import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

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
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void queryBills_shouldReturnQueryBillsResponse_whenProcessManagerExecutesSuccessfully() throws MicroException {
        // Given
        QueryBillsRequest request = new QueryBillsRequest();
        QueryBillProcessInput processInput = new QueryBillProcessInput();
        QueryBillsProcessOutput processOutput = new QueryBillsProcessOutput();
        QueryBillsResponse expectedResponse = new QueryBillsResponse();

        when(processExecutionMapper.toQueryBillProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(processExecutionMapper.toQueryBillsResponse(processOutput)).thenReturn(expectedResponse);

        // When
        QueryBillsResponse actualResponse = paymentService.queryBills(request);

        // Then
        assertEquals(expectedResponse, actualResponse);
        verify(processExecutionMapper).toQueryBillProcessInput(request);
        verify(requestContext).getChannelSessionId();
        verify(requestContext).getChannelTransactionId();
        verify(processManager).executeProcess(processInput);
        verify(processExecutionMapper).toQueryBillsResponse(processOutput);
    }
}

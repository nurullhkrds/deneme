@RunWith(MockitoJUnitRunner.class)
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

    @Test
    public void queryBills_ShouldReturnQueryBillsResponse() throws MicroException {
        // Arrange
        QueryBillsRequest request = new QueryBillsRequest();
        QueryBillProcessInput processInput = new QueryBillProcessInput();
        QueryBillsProcessOutput processOutput = new QueryBillsProcessOutput();
        QueryBillsResponse expectedResponse = new QueryBillsResponse();

        when(processExecutionMapper.toQueryBillProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(processExecutionMapper.toQueryBillsResponse(processOutput)).thenReturn(expectedResponse);

        // Act
        QueryBillsResponse actualResponse = paymentService.queryBills(request);

        // Assert
        assertEquals(expectedResponse, actualResponse);
        verify(processManager).executeProcess(processInput);
    }

    @Test
    public void getCustomerPaidBillList_ShouldReturnCombinedBillList() throws MicroException {
        // Arrange
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> combinedBillList = new ArrayList<>();

        when(paymentRepository.findCustomerPaidBillList(any(), any(), any(), any())).thenReturn(new ArrayList<>());
        when(billPaymentRestFacade.getCustomerPaidBillList(any())).thenReturn(new ResponseGetCustomerPaidBillList(SUCCESS, new ArrayList<>()));

        // Act
        GetCustomerPaidBillListResponse response = paymentService.getCustomerPaidBillList(request);

        // Assert
        assertNotNull(response);
        assertEquals(combinedBillList, response.getBillList());
    }

    @Test
    public void doBillPayment_ShouldReturnDoBillPaymentResponse() throws MicroException {
        // Arrange
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        BillPaymentProcessInput processInput = new BillPaymentProcessInput();
        BillPaymentProcessOutput processOutput = new BillPaymentProcessOutput();
        DoBillPaymentResponse expectedResponse = new DoBillPaymentResponse();

        when(processExecutionMapper.toBillPaymentProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(processOutput.getBillId()).thenReturn("billId");
        when(processOutput.getContractNo()).thenReturn("contractNo");

        // Act
        DoBillPaymentResponse actualResponse = paymentService.doBillPayment(request);

        // Assert
        assertEquals(expectedResponse, actualResponse);
        verify(processManager).executeProcess(processInput);
    }

    @Test
    public void cancelBillPayment_ShouldReturnCancelBillPaymentResponse() throws MicroException {
        // Arrange
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        BillPaymentReverseProcessInput processInput = new BillPaymentReverseProcessInput();
        CancelBillPaymentResponse expectedResponse = new CancelBillPaymentResponse();

        when(processExecutionMapper.toBillPaymentReverseProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");

        // Act
        CancelBillPaymentResponse actualResponse = paymentService.cancelBillPayment(request);

        // Assert
        assertNotNull(actualResponse);
        verify(processManager).executeProcess(processInput);
    }

    @Test
    public void insertPayment_ShouldReturnPaymentDTO() {
        // Arrange
        PaymentDTO paymentDTO = new PaymentDTO();
        Payment paymentEntity = new Payment();

        when(paymentMapper.toEntity(paymentDTO)).thenReturn(paymentEntity);
        when(paymentRepository.save(paymentEntity)).thenReturn(paymentEntity);
        when(paymentMapper.toDTO(paymentEntity)).thenReturn(paymentDTO);

        // Act
        PaymentDTO actualDTO = paymentService.insertPayment(paymentDTO);

        // Assert
        assertEquals(paymentDTO, actualDTO);
    }

    @Test
    public void getPayment_ShouldReturnPaymentDTO() {
        // Arrange
        Long id = 1L;
        Long contractNo = 123L;
        Payment payment = new Payment();
        PaymentDTO paymentDTO = new PaymentDTO();

        when(paymentRepository.findByIdAndContractNo(id, contractNo)).thenReturn(Optional.of(payment));
        when(paymentMapper.toDTO(payment)).thenReturn(paymentDTO);

        // Act
        PaymentDTO actualDTO = paymentService.getPayment(id, contractNo);

        // Assert
        assertEquals(paymentDTO, actualDTO);
    }

    @Test
    @Transactional
    public void updateStatus_ShouldUpdateStatus() {
        // Arrange
        EnumBillStatu status = EnumBillStatu.PAID;
        Long id = 1L;
        Long contractNo = 123L;

        // Act
        paymentService.updateStatus(status, id, contractNo);

        // Assert
        verify(paymentRepository).updateStatus(status.getValue(), id, contractNo);
    }

    @Test
    public void insertPaymentCancel_ShouldReturnPaymentCancelDTO() {
        // Arrange
        PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
        PaymentCancel paymentCancelEntity = new PaymentCancel();

        when(paymentCancelMapper.toEntity(paymentCancelDTO)).thenReturn(paymentCancelEntity);
        when(paymentCancelRepository.save(paymentCancelEntity)).thenReturn(paymentCancelEntity);
        when(paymentCancelMapper.toDTO(paymentCancelEntity)).thenReturn(paymentCancelDTO);

        // Act
        PaymentCancelDTO actualDTO = paymentService.insertPaymentCancel(paymentCancelDTO);

        // Assert
        assertEquals(paymentCancelDTO, actualDTO);
    }

    @Test
    public void parseSubscriberNoIntoParts_ShouldReturnParsedParts() {
        // Arrange
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        List<InstitutionUserIntfDTO> institutionUserIntfDTOList = new ArrayList<>();
        List<SubsrciberNoPartResponseWebDTO> parsedParts = new ArrayList<>();

        when(institutionUserIntService.getDefaultUserInterface(any(), any())).thenReturn(institutionUserIntfDTOList);
        when(SubscriberNumberUtils.parseSubscriberNoIntoParts(institutionUserIntfDTOList, request.getSubscriberNo())).thenReturn(parsedParts);

        // Act
        ParseSubscriberNoIntoPartsResponse response = paymentService.parseSubscriberNoIntoParts(request);

        // Assert
        assertNotNull(response);
        assertEquals(parsedParts, response.getSubsrciberNoPartResponseWebDTO());
    }

    @Test
    public void findPaymentByIdWithLock_ShouldReturnPayment() {
        // Arrange
        Long paymentId = 1L;
        Payment payment = new Payment();

        when(paymentRepository.findByIdWithLock(paymentId)).thenReturn(Optional.of(payment));

        // Act
        Payment actualPayment = paymentService.findPaymentByIdWithLock(paymentId);

        // Assert
        assertNotNull(actualPayment);
    }

    @Test
    @Transactional
    public void updatePayment_ShouldSavePayment() {
        // Arrange
        Payment payment = new Payment();

        // Act
        paymentService.updatePayment(payment);

        // Assert
        verify(paymentRepository).save(payment);
    }

    @Test
    public void getPayment_ShouldReturnPaymentDTO() {
        // Arrange
        Long paymentId = 1L;
        Payment payment = new Payment();
        PaymentDTO paymentDTO = new PaymentDTO();

        when(paymentRepository.findById(paymentId)).thenReturn(Optional.of(payment));
        when(paymentMapper.toDTO(payment)).thenReturn(paymentDTO);

        // Act
        PaymentDTO actualDTO = paymentService.getPayment(paymentId);

        // Assert
        assertEquals(paymentDTO, actualDTO);
    }

    @Test
    @Transactional(rollbackFor = Exception.class)
    public void notifyPayment_ShouldReturnNotifyPaymentResponse() throws MicroException {
        // Arrange
        NotifyPaymentRequest request = new NotifyPaymentRequest();
        NotifyPaymentProcessInput processInput = new NotifyPaymentProcessInput();
        NotifyPaymentProcessOutput processOutput = new NotifyPaymentProcessOutput();
        NotifyPaymentResponse expectedResponse = new NotifyPaymentResponse();

        when(processExecutionMapper.toNotifyPaymentProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(paymentMapper.toNotifyPaymentResponse(processOutput)).thenReturn(expectedResponse);

        // Act
        NotifyPaymentResponse actualResponse = paymentService.notifyPayment(request);

        // Assert
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    @Transactional(rollbackFor = Exception.class)
    public void processPayment_ShouldReturnProcessPaymentResponse() throws MicroException {
        // Arrange
        ProcessPaymentRequest request = new ProcessPaymentRequest();
        ProcessPaymentProcessInput processInput = new ProcessPaymentProcessInput();
        ProcessPaymentProcessOutput processOutput = new ProcessPaymentProcessOutput();
        ProcessPaymentResponse expectedResponse = new ProcessPaymentResponse();

        when(processExecutionMapper.toProcessPaymentProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);
        when(paymentMapper.toProcessPaymentResponse(processOutput)).thenReturn(expectedResponse);

        // Act
        ProcessPaymentResponse actualResponse = paymentService.processPayment(request);

        // Assert
        assertEquals(expectedResponse, actualResponse);
    }
}

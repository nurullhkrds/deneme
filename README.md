public class PaymentNotificationControllerTest {

    @InjectMocks
    private PaymentNotificationController paymentNotificationController;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testSendCreditCardProvisionAck() throws BusinessException {
        // Arrange
        GetCreditCardProvisionAckRequest request = new GetCreditCardProvisionAckRequest();
        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        request.setPaymentDTO(paymentDTO);
        request.setPaymentNotificationId("notificationId");
        request.setIsBatch(true);

        doNothing().when(paymentNotificationService).sendAckForCreditCardProvision(anyLong(), anyString(), anyBoolean());

        // Act
        ResponseEntity<Void> response = paymentNotificationController.sendCreditCardProvisionAck(request);

        // Assert
        assertEquals(HttpStatus.OK, response.getStatusCode());
        verify(paymentNotificationService).sendAckForCreditCardProvision(123L, "notificationId", true);
    }

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<String> handleBusinessException(BusinessException ex) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(ex.getMessage());
    }
}

ExtendWith(MockitoExtension.class)
public class PaymentEventListenerTest {

    @Mock
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @Mock
    private LimitationService limitationService;

    @Mock
    private ApplicationContext applicationContext;

    @Mock
    private SpringUtil springUtil;

    @InjectMocks
    private PaymentEventListener paymentEventListener;

    private BillPaymentEvent billPaymentEvent;
    private BillPaymentCancelEvent billPaymentCancelEvent;
    private CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO;
    private CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO;
    private NotifyPaymentLimitationRequest notifyPaymentLimitationRequest;
    private NotifyInquiryLimitationRequest notifyInquiryLimitationRequest;

    @BeforeEach
    void setUp() {
        // SpringUtil appContext ayarlaması
        when(springUtil.getApplicationContext()).thenReturn(applicationContext);
        when(applicationContext.getBean(LimitationService.class)).thenReturn(limitationService);

        // Mock event objects
        billPaymentEvent = mock(BillPaymentEvent.class);
        billPaymentCancelEvent = mock(BillPaymentCancelEvent.class);
        creditCardProvisionACKEventDTO = mock(CreditCardProvisionACKEventDTO.class);
        creditCardProvisionReverseEventDTO = mock(CreditCardProvisionReverseEventDTO.class);
        notifyPaymentLimitationRequest = mock(NotifyPaymentLimitationRequest.class);
        notifyInquiryLimitationRequest = mock(NotifyInquiryLimitationRequest.class);

        // Mock necessary methods and behaviors
        PaymentDTO paymentDTO = mock(PaymentDTO.class);
        InstitutionDTO institutionDTO = mock(InstitutionDTO.class);
        ProductDTO productDTO = mock(ProductDTO.class);
        BillPaymentCancelDTO cancelRecord = mock(BillPaymentCancelDTO.class);

        when(billPaymentEvent.getPaymentDTO()).thenReturn(paymentDTO);
        when(billPaymentEvent.getInstitutionDTO()).thenReturn(institutionDTO);
        when(institutionDTO.getProduct()).thenReturn(productDTO);
        when(billPaymentCancelEvent.getCancelRecord()).thenReturn(cancelRecord);

        // Mock DTO methods
        when(paymentDTO.getCreatedBy()).thenReturn("testUser");
        when(paymentDTO.getBranchCode()).thenReturn("branch123");
        when(paymentDTO.getChannelCode()).thenReturn("channel456");
        when(paymentDTO.getChannelSessionId()).thenReturn("session789");
        when(paymentDTO.getChannelTransactionId()).thenReturn("txn123");
        when(paymentDTO.getInstitutionId()).thenReturn(1L);
        when(institutionDTO.getInstitutionCode()).thenReturn("instCode");
        when(productDTO.getCode()).thenReturn("prodCode");
        when(cancelRecord.getCreatedBy()).thenReturn("cancelUser");
    }

    @Test
    void testOnPaymentCreatedNotificationEvent() {
        paymentEventListener.onPaymentCreatedNotificationEvent(billPaymentEvent);

        verify(paymentNotificationEventProducer, times(1)).sendPaymentNotificationEvent(any(PaymentNotificationEvent.class));
    }

    @Test
    void testOnPaymentCancelCreatedNotificationEvent() {
        paymentEventListener.onPaymentCancelCreatedNotificationEvent(billPaymentCancelEvent);

        verify(paymentNotificationEventProducer, times(1)).sendPaymentCancelNotificationEvent(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    void testOnCreditCardProvisionACKEvent() {
        paymentEventListener.onCreditCardProvisionACKEvent(creditCardProvisionACKEventDTO);

        verify(paymentNotificationEventProducer, times(1)).sendCreditCardProvisionACKEvent(any(CreditCardProvisionACKEventDTO.class));
    }

    @Test
    void testOnCreditCardProvisionReverseEvent() {
        paymentEventListener.onCreditCardProvisionReverseEvent(creditCardProvisionReverseEventDTO);

        verify(paymentNotificationEventProducer, times(1)).sendCreditCardProvisionReverseEvent(any(CreditCardProvisionReverseEventDTO.class));
    }

    @Test
    void testOnNotifyPaymentLimitation() {
        paymentEventListener.onNotifyPaymentLimitation(notifyPaymentLimitationRequest);

        verify(limitationService, times(1)).notifyPaymentLimitation(any(NotifyPaymentLimitationRequest.class));
    }

    @Test
    void testOnNotifyInquiryLimitation() {
        paymentEventListener.onNotifyPaymentLimitation(notifyInquiryLimitationRequest);

        verify(limitationService, times(1)).notifyInquiryLimitation(any(NotifyInquiryLimitationRequest.class));
    }
}

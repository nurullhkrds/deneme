@ExtendWith(MockitoExtension.class)
public class PaymentEventPublisherTest {

    @Mock
    private ApplicationEventPublisher eventPublisher;

    @InjectMocks
    private PaymentEventPublisher paymentEventPublisher;

    @Mock
    private PublishPaymentTypeDTO publishPaymentTypeDTO;

    @Mock
    private PaymentNotificationDTO paymentNotificationDTO;

    @Mock
    private PaymentDTO paymentDTO;

    @Mock
    private InstitutionDTO institutionDTO;

    @Mock
    private PaymentMethod paymentMethod;

    @BeforeEach
    public void setup() {
        when(publishPaymentTypeDTO.getPaymentDTO()).thenReturn(paymentDTO);
        when(publishPaymentTypeDTO.getInstitutionDTO()).thenReturn(institutionDTO);
        when(paymentDTO.getPaymentMethod()).thenReturn(paymentMethod);
    }

    @Test
    public void testFindPublishPaymentEvent() {
        // Test for INSTITUTION_PAYMENT_NOTIFICATION
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(BillPaymentEvent.class));

        // Test for CRD_PRVSN_ACK
        when(paymentNotificationDTO.getNotificationType())
                .thenReturn(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        when(paymentMethod.getProvisionType()).thenReturn(EnumProvisionType.CARD);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }

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
    @Mock
    private PaymentCancelDTO paymentCancelDTO;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
        paymentDTO = new PaymentDTO();
        institutionDTO = new InstitutionDTO();
        paymentNotificationDTO = new PaymentNotificationDTO();
        paymentCancelDTO = new PaymentCancelDTO();
        publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
        publishPaymentTypeDTO.setInstitutionDTO(institutionDTO);
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Collections.singletonList(paymentNotificationDTO));

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
        when(paymentMethod.getCode()).thenReturn(EnumPaymentMethod.CARD);
        when(publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList())
                .thenReturn(List.of(paymentNotificationDTO));

        paymentEventPublisher.findPublishPaymentEvent(publishPaymentTypeDTO);

        verify(eventPublisher, times(1)).publishEvent(any(CreditCardProvisionACKEventDTO.class));
    }

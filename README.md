
    @Mock
    private ApplicationEventPublisher eventPublisher;

    @InjectMocks
    private PaymentEventPublisher paymentEventPublisher;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPublishCreditCardProvisionReverseEvent() {
        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setId(1L);
        PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
        paymentCancelDTO.setId(2L);
        Long paymentNotificationID = 3L;

        paymentEventPublisher.publishCreditCardProvisionReverseEvent(paymentDTO, paymentCancelDTO, paymentNotificationID);

        ArgumentCaptor<CreditCardProvisionReverseEventDTO> eventCaptor = ArgumentCaptor.forClass(CreditCardProvisionReverseEventDTO.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        CreditCardProvisionReverseEventDTO event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(paymentDTO.getId(), event.getPaymentId());
        assertEquals(paymentCancelDTO.getId(), event.getPaymentCancelId());
        assertEquals(paymentNotificationID, event.getPaymentNotificationId());
    }

    @Test
    void testPublishPaymentLimiationNotification() {
        NotifyPaymentLimitationRequest request = new NotifyPaymentLimitationRequest();

        paymentEventPublisher.publishPaymentLimiationNotification(request);

        verify(eventPublisher).publishEvent(request);
    }

    @Test
    void testPublishInquiryLimiationNotification() {
        NotifyInquiryLimitationRequest request = new NotifyInquiryLimitationRequest();

        paymentEventPublisher.publishInquiryLimiationNotification(request);

        verify(eventPublisher).publishEvent(request);
    }

    @Test
    void testFindPublishPaymentEventV2() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        PaymentNotificationDTO notification1 = new PaymentNotificationDTO();
        notification1.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        PaymentNotificationDTO notification2 = new PaymentNotificationDTO();
        notification2.setNotificationType(EnumPaymentNotificationType.CRD_PRVSN_ACK);
        
        publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(Arrays.asList(notification1, notification2));
        publishPaymentTypeDTO.setPaymentDTO(new PaymentDTO());
        publishPaymentTypeDTO.setInstitutionDTO(new InstitutionDTO());

        paymentEventPublisher.findPublishPaymentEventV2(publishPaymentTypeDTO);

        verify(eventPublisher, times(2)).publishEvent(any());
    }

    @Test
    void testPublishPaymentNotificationEventV2() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        PaymentNotificationDTO notification = new PaymentNotificationDTO();
        notification.setId(1L);

        publishPaymentTypeDTO.setPaymentDTO(new PaymentDTO());
        publishPaymentTypeDTO.setInstitutionDTO(new InstitutionDTO());

        paymentEventPublisher.publishPaymentNotificationEventV2(publishPaymentTypeDTO, notification);

        ArgumentCaptor<BillPaymentEvent> eventCaptor = ArgumentCaptor.forClass(BillPaymentEvent.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        BillPaymentEvent event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(publishPaymentTypeDTO.getPaymentDTO(), event.getPaymentDTO());
        assertEquals(publishPaymentTypeDTO.getInstitutionDTO(), event.getInstitutionDTO());
        assertEquals(notification.getId(), event.getPaymentNotificationId());
    }

    @Test
    void testPublishCreditCardProvisionACKEventV2() {
        PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
        PaymentNotificationDTO notification = new PaymentNotificationDTO();
        notification.setId(1L);

        PaymentDTO paymentDTO = new PaymentDTO();
        paymentDTO.setId(2L);
        publishPaymentTypeDTO.setPaymentDTO(paymentDTO);

        paymentEventPublisher.publishCreditCardProvisionACKEventV2(publishPaymentTypeDTO, notification);

        ArgumentCaptor<CreditCardProvisionACKEventDTO> eventCaptor = ArgumentCaptor.forClass(CreditCardProvisionACKEventDTO.class);
        verify(eventPublisher).publishEvent(eventCaptor.capture());

        CreditCardProvisionACKEventDTO event = eventCaptor.getValue();
        assertNotNull(event);
        assertEquals(paymentDTO.getId(), event.getPaymentId());
        assertEquals(notification.getId(), event.getPaymentNotificationId());
    }

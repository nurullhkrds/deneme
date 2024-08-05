@ExtendWith(MockitoExtension.class)
class PaymentNotificationEventProducerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @InjectMocks
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    private static final String PAYMENT_NOTIFICATION_EXCHANGE = "paymentNotificationEventExchangeName";
    private static final String PAYMENT_NOTIFICATION_ROUTING_KEY = "paymentNotificationEventRoutingKey";
    private static final String PAYMENT_CANCEL_EXCHANGE = "paymentCancelNotificationEventExchangeName";
    private static final String PAYMENT_CANCEL_ROUTING_KEY = "paymentCancelNotificationEventRoutingKey";
    private static final String CREDIT_CARD_ACK_EXCHANGE = "creditCardProvisionACKEventExchangeName";
    private static final String CREDIT_CARD_ACK_ROUTING_KEY = "creditCardProvisionACKEventRoutingKey";
    private static final String CREDIT_CARD_REVERSE_EXCHANGE = "creditCardProvisionReverseEventExchangeName";
    private static final String CREDIT_CARD_REVERSE_ROUTING_KEY = "ccreditCardProvisionReverseEventRoutingKey";

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testSendPaymentNotificationEvent_Success() throws InterruptedException {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId("123");

        doNothing().when(rabbitTemplate).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentNotificationEvent_Failure() throws InterruptedException {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        // Istisnanın fırlatıldığını doğrulamak için bir flag kullanabilirsiniz
        boolean exceptionThrown = false;
        try {
            paymentNotificationEventProducer.sendPaymentNotificationEvent(event);
        } catch (Exception e) {
            exceptionThrown = true;
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_NOTIFICATION_EXCHANGE, PAYMENT_NOTIFICATION_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Success() throws InterruptedException {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId("123");

        doNothing().when(rabbitTemplate).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Failure() throws InterruptedException {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        boolean exceptionThrown = false;
        try {
            paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);
        } catch (Exception e) {
            exceptionThrown = true;
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(PAYMENT_CANCEL_EXCHANGE, PAYMENT_CANCEL_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Success() throws InterruptedException {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("123");

        doNothing().when(rabbitTemplate).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Failure() throws InterruptedException {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        boolean exceptionThrown = false;
        try {
            paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);
        } catch (Exception e) {
            exceptionThrown = true;
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_ACK_EXCHANGE, CREDIT_CARD_ACK_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Success() throws InterruptedException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId("123");

        doNothing().when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() throws InterruptedException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId("123");

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
        latch.await(1, TimeUnit.SECONDS);

        boolean exceptionThrown = false;
        try {
            paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
        } catch (Exception e) {
            exceptionThrown = true;
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }
}

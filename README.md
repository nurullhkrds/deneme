
@ExtendWith(MockitoExtension.class)
public class PaymentNotificationEventProducerTest {

    @Mock
    private RabbitTemplate rabbitTemplate;

    @Mock
    private org.slf4j.Logger logger;

    @InjectMocks
    private PaymentNotificationEventProducer paymentNotificationEventProducer;

    @BeforeEach
    public void setUp() {
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventExchangeName", "paymentNotificationEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentNotificationEventRoutingKey", "paymentNotificationEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventExchangeName", "paymentCancelNotificationEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "paymentCancelNotificationEventRoutingKey", "paymentCancelNotificationEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventExchangeName", "creditCardProvisionACKEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionACKEventRoutingKey", "creditCardProvisionACKEventRoutingKey");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "creditCardProvisionReverseEventExchangeName", "creditCardProvisionReverseEventExchange");
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "ccreditCardProvisionReverseEventRoutingKey", "creditCardProvisionReverseEventRoutingKey");
        
        ReflectionTestUtils.setField(paymentNotificationEventProducer, "logger", logger);
    }

    @Test
    public void testSendPaymentNotificationEvent_Success() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);

        verify(rabbitTemplate).convertAndSend(eq("paymentNotificationEventExchange"), eq("paymentNotificationEventRoutingKey"), eq(event));
        verify(logger).info(eq("Published payment notification message. paymentNotificationId: '{}'"), eq(123L));
    }

    @Test
    public void testSendPaymentNotificationEvent_Failure() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);

        verify(logger).error(eq("Error occurred while publishing payment notification message. paymentNotificationId: '{}' Exception Detail: '{}'"),
                eq(123L), any(String.class));
    }

    @Test
    public void testSendPaymentCancelNotificationEvent_Success() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);

        verify(rabbitTemplate).convertAndSend(eq("paymentCancelNotificationEventExchange"), eq("paymentCancelNotificationEventRoutingKey"), eq(event));
        verify(logger).info(eq("Published payment notification message. paymentNotificationId: '{}'"), eq(123L));
    }

    @Test
    public void testSendPaymentCancelNotificationEvent_Failure() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event);

        verify(logger).error(eq("Error occurred while publishing payment notification message. paymentNotificationId: '{}' Exception Detail: '{}'"),
                eq(123L), any(String.class));
    }

    @Test
    public void testSendCreditCardProvisionACKEvent_Success() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionACKEventExchange"), eq("creditCardProvisionACKEventRoutingKey"), eq(event));
        verify(logger).info(eq("Published credit card provision ack message. Payment Id: '{}'"), eq(123L));
    }

    @Test
    public void testSendCreditCardProvisionACKEvent_Failure() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event);

        verify(logger).error(eq("Error occurred while publishing credit card provision ack message. Payment Id: '{}' Exception Detail: '{}'"),
                eq(123L), any(String.class));
    }

    @Test
    public void testSendCreditCardProvisionReverseEvent_Success() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionReverseEventExchange"), eq("creditCardProvisionReverseEventRoutingKey"), eq(event));
        verify(logger).info(eq("Published credit card provision reverse message. Payment Cancel Id: '{}'"), eq(123L));
    }

    @Test
    public void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);

        verify(logger).error(eq("Error occurred while publishing credit card provision ack message. Payment Cancel Id: '{}' Exception Detail: '{}'"),
                eq(123L), any(String.class));
    }
}

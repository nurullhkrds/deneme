@Test
void testSendPaymentNotificationEvent_Failure() {
    PaymentNotificationEvent event = new PaymentNotificationEvent();
    event.setPaymentNotificationId(123L);

    // Assert that a RuntimeException is thrown
    assertThrows(RuntimeException.class, () -> {
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));
        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);
    });
}

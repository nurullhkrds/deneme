@Test
void testSendPaymentNotificationEvent_Failure() {
    PaymentNotificationEvent event = new PaymentNotificationEvent();
    event.setPaymentNotificationId(123L);

    // Arrange: Set up the mock to throw an exception when the method is called
    doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

    // Act and Assert: Assert that a RuntimeException is thrown when sendPaymentNotificationEvent is called
    assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));

    // Verify that convertAndSend was called
    verify(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));
}

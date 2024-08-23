    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(eq("creditCardProvisionReverseEventExchange"), eq("creditCardProvisionReverseEventRoutingKey"), eq(event));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));
    }org.opentest4j.AssertionFailedError: Expected java.lang.RuntimeException to be thrown, but nothing was thrown.

	at org.junit.jupiter.api.AssertThrows.assertThrows(AssertThrows.java:71)
	at org.junit.jupiter.api.AssertThrows.assertThrows(AssertThrows.java:37)
	at org.junit.jupiter.api.Assertions.assertThrows(Assertions.java:3082)

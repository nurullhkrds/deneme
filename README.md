@Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        // Verify that the method throws the expected exception
        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));
    }

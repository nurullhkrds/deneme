SonarLint: Add at least one assertion to this test case.

   @Test
     void testSendPaymentNotificationEvent_Failure() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(any(String.class), any(String.class), any(Object.class));

        paymentNotificationEventProducer.sendPaymentNotificationEvent(event);

    }

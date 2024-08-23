 @Test
    void testSendPaymentNotificationEvent_Success() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("paymentNotificationEventExchange"), eq("paymentNotificationEventRoutingKey"), eq(event));
    }

    @Test
    void testSendPaymentNotificationEvent_Failure() {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        event.setPaymentNotificationId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(eq("paymentNotificationEventExchange"), eq("paymentNotificationEventRoutingKey"), eq(event));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentNotificationEvent(event));
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Success() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("paymentCancelNotificationEventExchange"), eq("paymentCancelNotificationEventRoutingKey"), eq(event));
    }

    @Test
    void testSendPaymentCancelNotificationEvent_Failure() {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        event.setPaymentNotificationId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(eq("paymentCancelNotificationEventExchange"), eq("paymentCancelNotificationEventRoutingKey"), eq(event));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(event));
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Success() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionACKEventExchange"), eq("creditCardProvisionACKEventRoutingKey"), eq(event));
    }

    @Test
    void testSendCreditCardProvisionACKEvent_Failure() {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(eq("creditCardProvisionACKEventExchange"), eq("creditCardProvisionACKEventRoutingKey"), eq(event));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(event));
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Success() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        assertDoesNotThrow(() -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));

        verify(rabbitTemplate).convertAndSend(eq("creditCardProvisionReverseEventExchange"), eq("creditCardProvisionReverseEventRoutingKey"), eq(event));
    }

    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        // Mock the convertAndSend method to throw an exception
        doThrow(new RuntimeException("Exception")).when(rabbitTemplate).convertAndSend(eq("creditCardProvisionReverseEventExchange"), eq("creditCardProvisionReverseEventRoutingKey"), eq(event));

        assertThrows(RuntimeException.class, () -> paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event));
    }

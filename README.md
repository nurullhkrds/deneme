
    @Test
    void testSendCreditCardProvisionReverseEvent_Failure() throws InterruptedException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        boolean exceptionThrown = false;

        try {
            paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
            latch.await(1, TimeUnit.SECONDS); // Asenkron işlemin tamamlanmasını bekleyin
        } catch (Exception e) {
            exceptionThrown = true;
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

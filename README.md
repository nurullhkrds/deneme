 @Test
    void testSendCreditCardProvisionReverseEvent_Failure() throws InterruptedException, ExecutionException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        Future<?> future = Executors.newSingleThreadExecutor().submit(() -> {
            try {
                paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
            } finally {
                latch.countDown();
            }
        });

        latch.await(1, TimeUnit.SECONDS);
        boolean exceptionThrown = false;
        try {
            future.get();
        } catch (ExecutionException e) {
            exceptionThrown = true;
            assertTrue(e.getCause() instanceof RuntimeException);
            assertEquals("RabbitMQ error", e.getCause().getMessage());
        }

        assertTrue(exceptionThrown);
        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

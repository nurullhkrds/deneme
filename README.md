 @Test
    void testSendCreditCardProvisionReverseEvent_Failure() throws InterruptedException, TimeoutException {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CountDownLatch latch = new CountDownLatch(1);
        boolean[] exceptionThrown = {false};

        Executors.newSingleThreadExecutor().submit(() -> {
            try {
                paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
            } catch (Exception e) {
                exceptionThrown[0] = true;
            } finally {
                latch.countDown();
            }
        });

        if (!latch.await(2, TimeUnit.SECONDS)) {
            throw new TimeoutException("Test timed out while waiting for async method to complete.");
        }

        assertTrue(exceptionThrown[0], "Expected an exception to be thrown, but it was not.");
        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

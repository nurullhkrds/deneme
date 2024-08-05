   @Test
    void testSendCreditCardProvisionReverseEvent_Failure() {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentCancelId(123L);

        doThrow(new RuntimeException("RabbitMQ error")).when(rabbitTemplate).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);

        CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
            try {
                paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(event);
            } catch (Exception ignored) {
                // Ignored
            }
        });

        ExecutionException thrownException = assertThrows(ExecutionException.class, future::get);
        assertTrue(thrownException.getCause() instanceof RuntimeException);
        assertEquals("RabbitMQ error", thrownException.getCause().getMessage());

        verify(rabbitTemplate, times(1)).convertAndSend(CREDIT_CARD_REVERSE_EXCHANGE, CREDIT_CARD_REVERSE_ROUTING_KEY, event);
    }

   @Test
    void testBillTransactionRabbitTemplate() throws Exception {
        // Mock the jsonMessageConverter method
        when(config.jsonMessageConverter()).thenReturn(messageConverter);

        // Use ReflectionTestUtils to invoke the private declareQueues method
        doAnswer(invocation -> {
            ReflectionTestUtils.invokeMethod(config, "declareQueues", connectionFactory);
            return null;
        }).when(config).billTransactionRabbitTemplate(connectionFactory);

        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertEquals(messageConverter, rabbitTemplate.getMessageConverter());
        assertFalse(rabbitTemplate.isChannelTransacted());

        // Verify that declareQueues was called using Reflection
        verify(config, times(1)).billTransactionRabbitTemplate(connectionFactory);
    }

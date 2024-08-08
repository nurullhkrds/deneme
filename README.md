
    @Test
    void testBillTransactionRabbitTemplate() throws Exception {
        // Mock the jsonMessageConverter method
        when(config.jsonMessageConverter()).thenReturn(messageConverter);

        // Mock the RabbitMQUtil.getChannel method to return a mock channel
        try (MockedStatic<RabbitMQUtil> mockedStatic = mockStatic(RabbitMQUtil.class)) {
            mockedStatic.when(() -> RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);

            RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

            assertNotNull(rabbitTemplate);
            assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
            assertEquals(messageConverter, rabbitTemplate.getMessageConverter());
            assertFalse(rabbitTemplate.isChannelTransacted());

            // Verify that declareQueue was called
            verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), anyMap());
        }
    }

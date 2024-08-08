
    @Test
    void testBillTransactionRabbitTemplate() throws IOException, TimeoutException {
        // Mock the jsonMessageConverter method
        when(config.jsonMessageConverter()).thenReturn(messageConverter);

        // Mock the declareQueues method
        doNothing().when(config).declareQueues(connectionFactory);

        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);

        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertEquals(messageConverter, rabbitTemplate.getMessageConverter());
        assertFalse(rabbitTemplate.isChannelTransacted());

        // Verify that declareQueues was called
        verify(config, times(1)).declareQueues(connectionFactory);
    }

  @Test
    public void testBillTransactionRabbitTemplate() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        Connection connection = mock(Connection.class);
        Channel channel = mock(Channel.class);

        given(connectionFactory.createConnection()).willReturn(connection);
        given(connection.createChannel(false)).willReturn(channel);

        try (MockedStatic<RabbitMQUtil> utilities = Mockito.mockStatic(RabbitMQUtil.class)) {
            utilities.when(() -> RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);
            RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);
            assertNotNull(rabbitTemplate);
            assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
            assertNotNull(rabbitTemplate.getMessageConverter());
        }
    }

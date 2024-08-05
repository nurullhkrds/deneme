   @Test
    public void testBillTransactionRabbitTemplate() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        RabbitTemplate rabbitTemplate = config.billTransactionRabbitTemplate(connectionFactory);
        assertNotNull(rabbitTemplate);
        assertEquals(connectionFactory, rabbitTemplate.getConnectionFactory());
        assertNotNull(rabbitTemplate.getMessageConverter());
    }
java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.common.rabbitmq.model.ServiceSpec.getQueues()" because the return value of "com.ykb.payments.bill.common.rabbitmq.model.RabbitMQProperties.getServiceByKey(String)" is null

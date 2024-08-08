    @Test
    void testCreditCardReverseProvisionRabbitListenerContainerFactory() {
        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("creditCardProvisionReverseEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.creditCardReverseProvisionRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
    }java.lang.NullPointerException: Cannot invoke "java.lang.Integer.intValue()" because "this.creditCardReverseProvisionNotificationMaxTryCount" is null

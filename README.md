java.lang.NullPointerException: Cannot invoke "java.lang.Integer.intValue()" because "this.creditCardProvisionACKNotificationMaxTryCount" is null
    @Test
    void testCreditCardProvisionACKRabbitListenerContainerFactory() {

        ConnectionFactory connectionFactory = mock(ConnectionFactory.class);
        when(rabbitMQProperties.getConsumerByKey(any(), eq("creditCardProvisionACKEvent"))).thenReturn(consumerSpec);

        SimpleRabbitListenerContainerFactory factory = config.creditCardProvisionACKRabbitListenerContainerFactory(connectionFactory);

        assertNotNull(factory);
    }

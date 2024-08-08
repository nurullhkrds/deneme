    @Test
    void testBillTransactionRabbitFactory() {
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getQueues()).thenReturn(Map.of("queueName", new QueueSpec()));
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getUsername()).thenReturn("user");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfCredentials.getString("vhost")).thenReturn("/");

        ConnectionFactory factory = config.billTransactionRabbitFactory();

        assertNotNull(factory);
        assertTrue(factory instanceof CachingConnectionFactory);
        CachingConnectionFactory cachingFactory = (CachingConnectionFactory) factory;
        assertEquals("localhost", cachingFactory.getHost());
        assertEquals("user", cachingFactory.getUsername());
    }java.lang.IllegalArgumentException: No service with name [null] was found.

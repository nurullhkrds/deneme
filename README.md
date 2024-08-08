   @Test
    void testBillTransactionRabbitFactory() {
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getName()).thenReturn("testService");
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfService.getPlan()).thenReturn("testPlan");
        when(cfCredentials.getName()).thenReturn("testService");
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getUsername()).thenReturn("user");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfCredentials.getString("vhost")).thenReturn("/");

        // Simulate finding the service
        when(cfService.getName()).thenReturn("testService");
        when(new CfEnv().findServiceByName(anyString())).thenReturn(cfService);

        ConnectionFactory factory = config.billTransactionRabbitFactory();

        assertNotNull(factory);
        assertTrue(factory instanceof CachingConnectionFactory);
        CachingConnectionFactory cachingFactory = (CachingConnectionFactory) factory;
        assertEquals("localhost", cachingFactory.getHost());
        assertEquals("user", cachingFactory.getUsername());
    }

    @Test
    void testBillTransactionRabbitFactory() {
        // Mock dependencies and set return values
        when(rabbitMQProperties.getServiceByKey(anyString())).thenReturn(serviceSpec);
        when(serviceSpec.getName()).thenReturn("testService");
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfService.getPlan()).thenReturn("testPlan");
        when(cfCredentials.getName()).thenReturn("testService");
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getUsername()).thenReturn("user");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfCredentials.getString("vhost")).thenReturn("/");

        // Mock CfEnv to return the mock cfService
        try (MockedStatic<CfEnv> cfEnvMockedStatic = mockStatic(CfEnv.class)) {
            CfEnv cfEnv = mock(CfEnv.class);
            when(cfEnv.findServiceByName(anyString())).thenReturn(cfService);
            cfEnvMockedStatic.when(CfEnv::new).thenReturn(cfEnv);

            // Call the method under test
            ConnectionFactory factory = config.billTransactionRabbitFactory();

            // Assertions
            assertNotNull(factory);
            assertTrue(factory instanceof CachingConnectionFactory);
            CachingConnectionFactory cachingFactory = (CachingConnectionFactory) factory;
            assertEquals("localhost", cachingFactory.getHost());
            assertEquals("user", cachingFactory.getUsername());
            assertEquals("password", cachingFactory.getPassword());
            assertEquals("/", cachingFactory.getVirtualHost());
        }
    }

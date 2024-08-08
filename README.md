  @Test
    public void testBillTransactionRabbitFactory() {
        String serviceKey = "billtransaction-rabbitmq";

        RabbitMQProperties.ServiceSpec serviceSpec = new RabbitMQProperties.ServiceSpec();
        serviceSpec.setName(serviceKey);

        when(rabbitMQProperties.getServiceByKey(serviceKey)).thenReturn(serviceSpec);
        when(cfEnv.findServiceByName(anyString())).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfService.getPlan()).thenReturn("standard");

        when(cfCredentials.getName()).thenReturn("rabbitServiceName");
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getUsername()).thenReturn("user");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfCredentials.getString("vhost")).thenReturn("vhost");

        ConnectionFactory connectionFactory = config.billTransactionRabbitFactory();

        assert connectionFactory instanceof CachingConnectionFactory;
        CachingConnectionFactory cachingConnectionFactory = (CachingConnectionFactory) connectionFactory;
        assert cachingConnectionFactory.getHost().equals("localhost");
        assert cachingConnectionFactory.getUsername().equals("user");
        assert cachingConnectionFactory.getPassword().equals("password");
        assert cachingConnectionFactory.getVirtualHost().equals("vhost");

        verify(cfEnv, times(1)).findServiceByName(anyString());
        verify(cfService, times(1)).getCredentials();
        verify(cfService, times(1)).getPlan();
        verify(cfCredentials, times(1)).getName();
        verify(cfCredentials, times(1)).getHost();
        verify(cfCredentials, times(1)).getUsername();
        verify(cfCredentials, times(1)).getPassword();
        verify(cfCredentials, times(1)).getString("vhost");
    }

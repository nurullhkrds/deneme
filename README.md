 lenient().when(connectionFactory.createConnection()).thenReturn(connection);
        lenient().when(connection.createChannel()).thenReturn(channel);
        lenient().when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));
        lenient().when(RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);


    @Test
    public void testDeclareQueues() throws IOException, TimeoutException, NoSuchMethodException, IllegalAccessException, InvocationTargetException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        queueSpec.setDeclare(true);
        queueSpec.setExchange(new ExchangeSpec("testExchange", "direct", true));

        RabbitMQProperties.ServiceSpec serviceSpec = new RabbitMQProperties.ServiceSpec();
        serviceSpec.setQueues(Collections.singletonMap("testQueue", queueSpec));

        when(rabbitMQProperties.getServiceByKey("billtransaction-rabbitmq")).thenReturn(serviceSpec);

        Method declareQueuesMethod = BillTransactionRabbitMQConfig.class.getDeclaredMethod("declareQueues", ConnectionFactory.class);
        declareQueuesMethod.setAccessible(true);
        declareQueuesMethod.invoke(config, connectionFactory);

        ArgumentCaptor<String> queueNameCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> exchangeNameCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> routingKeyCaptor = ArgumentCaptor.forClass(String.class);

        verify(channel, times(1)).exchangeDeclare(exchangeNameCaptor.capture(), anyString(), anyBoolean());
        verify(channel, times(1)).queueDeclare(queueNameCaptor.capture(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(channel, times(1)).queueBind(queueNameCaptor.capture(), exchangeNameCaptor.capture(), routingKeyCaptor.capture());

        assert queueNameCaptor.getValue().equals("testQueue");
        assert exchangeNameCaptor.getValue().equals("testExchange");
        assert routingKeyCaptor.getValue() == null || routingKeyCaptor.getValue().isEmpty();
    }

  @Test
    public void testDeclareQueues() throws IOException, TimeoutException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        queueSpec.setDeclare(true);
        queueSpec.setExchange(new ExchangeSpec("testExchange", "direct", true));

        RabbitMQProperties.ServiceSpec serviceSpec = new RabbitMQProperties.ServiceSpec();
        serviceSpec.setQueues(Collections.singletonMap("testQueue", queueSpec));

        when(rabbitMQProperties.getServiceByKey("billtransaction-rabbitmq")).thenReturn(serviceSpec);
        when(RabbitMQUtil.getChannel(connectionFactory)).thenReturn(channel);
        when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));

        config.declareQueues(connectionFactory);

        verify(channel, times(1)).exchangeDeclare(anyString(), anyString(), anyBoolean());
        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(channel, times(1)).queueBind(anyString(), anyString(), anyString());
    }

    @Test
    public void testDeclareQueue() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        queueSpec.setExchange(new ExchangeSpec("testExchange", "direct", true));

        Method declareQueueMethod = BillTransactionRabbitMQConfig.class.getDeclaredMethod("declareQueue", Channel.class, QueueSpec.class);
        declareQueueMethod.setAccessible(true);

        when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));

        declareQueueMethod.invoke(config, channel, queueSpec);

        verify(channel, times(1)).exchangeDeclare(anyString(), anyString(), anyBoolean());
        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(channel, times(1)).queueBind(anyString(), anyString(), anyString());
    }

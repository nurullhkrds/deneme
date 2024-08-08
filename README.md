@Test
    public void testDeclareQueue() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        queueSpec.setExchange(new ExchangeSpec("testExchange", "direct", true));
        queueSpec.setRoutingKey("testRoutingKey");

        Method declareQueueMethod = BillTransactionRabbitMQConfig.class.getDeclaredMethod("declareQueue", Channel.class, QueueSpec.class);
        declareQueueMethod.setAccessible(true);

        when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));

        declareQueueMethod.invoke(config, channel, queueSpec);

        verify(channel, times(1)).exchangeDeclare("testExchange", "direct", true);
        verify(channel, times(1)).queueDeclare("testQueue", false, false, false, null);
        verify(channel, times(1)).queueBind("testQueue", "testExchange", "testRoutingKey");
    }

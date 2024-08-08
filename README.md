    @Test
    public void testCallQueueDeclare() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        ExchangeSpec exchangeSpec=new ExchangeSpec();
        exchangeSpec.setName("testExchange");
        exchangeSpec.setType("direct");
        exchangeSpec.setDurable(true);
        queueSpec.setExchange(exchangeSpec);
        Method callQueueDeclareMethod = BillTransactionRabbitMQConfig.class.getDeclaredMethod("callQueueDeclare", Channel.class, QueueSpec.class);
        callQueueDeclareMethod.setAccessible(true);

        when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));

        callQueueDeclareMethod.invoke(config, channel, queueSpec);

        verify(channel, times(1)).exchangeDeclare(anyString(), anyString(), anyBoolean());
        verify(channel, times(1)).queueDeclare(anyString(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(channel, times(1)).queueBind(anyString(), anyString(), anyString());
    }

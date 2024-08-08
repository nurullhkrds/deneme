 @Test
    public void testCallQueueDeclare() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException {
        QueueSpec queueSpec = new QueueSpec();
        queueSpec.setName("testQueue");
        ExchangeSpec exchangeSpec = new ExchangeSpec();
        exchangeSpec.setName("testExchange");
        exchangeSpec.setType("direct");
        exchangeSpec.setDurable(true);
        queueSpec.setExchange(exchangeSpec);
        queueSpec.setRoutingKey("");

        Method callQueueDeclareMethod = BillTransactionRabbitMQConfig.class.getDeclaredMethod("callQueueDeclare", Channel.class, QueueSpec.class);
        callQueueDeclareMethod.setAccessible(true);

        when(channel.queueDeclarePassive(anyString())).thenThrow(new IOException("Queue not found"));

        callQueueDeclareMethod.invoke(config, channel, queueSpec);

        ArgumentCaptor<String> queueNameCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> exchangeNameCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> routingKeyCaptor = ArgumentCaptor.forClass(String.class);

        verify(channel, times(1)).exchangeDeclare(exchangeNameCaptor.capture(), anyString(), anyBoolean());
        verify(channel, times(1)).queueDeclare(queueNameCaptor.capture(), anyBoolean(), anyBoolean(), anyBoolean(), any());
        verify(channel, times(1)).queueBind(queueNameCaptor.capture(), exchangeNameCaptor.capture(), routingKeyCaptor.capture());

        assert queueNameCaptor.getAllValues().get(0).equals("testQueue");
        assert exchangeNameCaptor.getValue().equals("testExchange");
        assert routingKeyCaptor.getValue() == null || routingKeyCaptor.getValue().isEmpty();
    }

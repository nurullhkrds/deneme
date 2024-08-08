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

Argument(s) are different! Wanted:
channel.queueBind(
    <any string>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfigTest.testCallQueueDeclare(BillTransactionRabbitMQConfigTest.java:122)
Actual invocations have different arguments:
channel.queueDeclarePassive(
    "testQueue"
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:248)
channel.exchangeDeclare(
    "testExchange",
    "direct",
    true
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:254)
channel.queueDeclare(
    "testQueue",
    false,
    false,
    false,
    null
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:257)
channel.queueBind(
    "testQueue",
    "testExchange",
    null
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:260)

Comparison Failure: 
<Click to see difference>

Argument(s) are different! Wanted:
channel.queueBind(
    <any string>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfigTest.testCallQueueDeclare(BillTransactionRabbitMQConfigTest.java:122)
Actual invocations have different arguments:
channel.queueDeclarePassive(
    "testQueue"
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:248)
channel.exchangeDeclare(
    "testExchange",
    "direct",
    true
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:254)
channel.queueDeclare(
    "testQueue",
    false,
    false,
    false,
    null
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:257)
channel.queueBind(
    "testQueue",
    "testExchange",
    null
);

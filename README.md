
Argument(s) are different! Wanted:
channel.queueBind(
    <any string>,
    <any string>,
    <any string>
);
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfigTest.testDeclareQueue(BillTransactionRabbitMQConfigTest.java:122)
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
-> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfigTest.testDeclareQueue(BillTransactionRabbitMQConfigTest.java:122)
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

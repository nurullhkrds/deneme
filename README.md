
org.mockito.exceptions.misusing.PotentialStubbingProblem: 
Strict stubbing argument mismatch. Please check:
 - this invocation of 'queueDeclarePassive' method:
    channel.queueDeclarePassive(null);
    -> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfig.callQueueDeclare(BillTransactionRabbitMQConfig.java:248)
 - has following stubbing(s) with different arguments:
    1. channel.queueDeclarePassive("");
      -> at com.ykb.payments.bill.transaction.config.BillTransactionRabbitMQConfigTest.testDeclareQueue(BillTransactionRabbitMQConfigTest.java:116)
Typically, stubbing argument mismatch indicates user mistake when writing tests.
Mockito fails early so that you can debug potential problem easily.
However, there are legit scenarios when this exception generates false negative signal:
  - stubbing the same method multiple times using 'given().will()' or 'when().then()' API
    Please use 'will().given()' or 'doReturn().when()' API for stubbing.
  - stubbed method is intentionally invoked with different arguments by code under test
    Please use default or 'silent' JUnit Rule (equivalent of Strictness.LENIENT).
For more information see javadoc for PotentialStubbingProblem class.

Wanted but not invoked:
logger.info(
    "Published payment notification message. paymentNotificationId: '{}'",
    123L
);
-> at com.ykb.payments.bill.transaction.payment.producer.PaymentNotificationEventProducerTest.testSendPaymentCancelNotificationEvent_Success(PaymentNotificationEventProducerTest.java:80)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
logger.info(
    "Published payment notification message. paymentNotificationId: '{}'",
    123L
);
-> at com.ykb.payments.bill.transaction.payment.producer.PaymentNotificationEventProducerTest.testSendPaymentCancelNotificationEvent_Success(PaymentNotificationEventProducerTest.java:80)
Actually, there were zero interactions with this mock.

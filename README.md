    @Test
    public void testProcessCreditCardProvisionReverseMessage_Success() throws Exception {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId(12345L);
        event.setPaymentNotificationId(12345L);
        event.setPaymentCancelId(12345L);

        doNothing().when(paymentNotificationService).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());

        billEventConsumer.processCreditCardProvisionReverseMessage(event);

        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());
    }


2024-08-03 10:53:50,082 INFO [main][BillEventConsumer] Processed credit card reverse provision message. Payment Cancel Id: '12345'


Wanted but not invoked:
paymentNotificationService.creditCardReverseProvision(
    <any long>,
    <any long>,
    <any long>,
    <any boolean>
);
-> at com.ykb.payments.bill.transaction.payment.consumer.BillEventConsumerTest.testProcessCreditCardProvisionReverseMessage_Success(BillEventConsumerTest.java:125)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
paymentNotificationService.creditCardReverseProvision(
    <any long>,
    <any long>,
    <any long>,
    <any boolean>
);
-> at com.ykb.payments.bill.transaction.payment.consumer.BillEventConsumerTest.testProcessCreditCardProvisionReverseMessage_Success(BillEventConsumerTest.java:125)
Actually, there were zero interactions with this mock.

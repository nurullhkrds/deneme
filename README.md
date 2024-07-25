java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.payment.dto.PaymentCancelDTO.getCreatedBy()" because the return value of "com.ykb.payments.bill.transaction.payment.model.BillPaymentCancelEvent.getCancelRecord()" is null
java.lang.NullPointerException: Cannot invoke "org.springframework.context.ApplicationContext.getBean(java.lang.Class)" because "com.ykb.payments.bill.common.util.SpringUtil.appContext" is null
rg.mockito.exceptions.misusing.UnnecessaryStubbingException: 
Unnecessary stubbings detected.
Clean & maintainable test code requires zero unnecessary code.
Following stubbings are unnecessary (click to navigate to relevant line of code):
  1. -> at com.ykb.payments.bill.transaction.payment.event.PaymentEventListenerTest.setUp(PaymentEventListenerTest.java:49)
  2. -> at com.ykb.payments.bill.transaction.payment.event.PaymentEventListenerTest.setUp(PaymentEventListenerTest.java:50)
Please remove unnecessary stubbings or use 'lenient' strictness. More info: javad
java.lang.NullPointerException: Cannot invoke "org.springframework.context.ApplicationContext.getBean(java.lang.Class)" because "com.ykb.payments.bill.common.util.SpringUtil.appContext" is null
java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.ProductDTO.getCode()" because the return value of "com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO.getProduct()" is null

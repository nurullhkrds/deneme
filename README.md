[ERROR] bill.remote.adapter.integration.BelediyeBuyukcekmeceIntegrationTest.givenNotifyBillPaymentCancelRequest_whenNotifyPaymentCancel_thenReturnNotifyBillPaymentCancelResponse -- Time elapsed: 0.012 s <<< ERROR!
org.mockito.exceptions.base.MockitoException: 

For common.util.SpringUtil, static mocking is already registered in the current thread

To create a new mock, the existing static mock registration must be deregistered
	at com.ykb.payments.bill.remote.adapter.integration.CommonIntegrationTest.setUp(CommonIntegrationTest.java:144)

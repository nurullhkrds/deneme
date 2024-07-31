    @Test
    void testOnNotifyPaymentLimitation() {
        NotifyPaymentLimitationRequest request = mock(NotifyPaymentLimitationRequest.class);

        ReflectionTestUtils.setField(paymentEventListener, "limitationService", limitationService);

        paymentEventListener.onNotifyPaymentLimitation(request);

        verify(limitationService, times(1)).notifyPaymentLimitation(request);
    }



java.lang.NullPointerException: Cannot invoke "org.springframework.context.ApplicationContext.getBean(java.lang.Class)" because "com.ykb.payments.bill.common.util.SpringUtil.appContext" is null

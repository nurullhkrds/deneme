    @Test
    void testOnNotifyInquiryLimitation() {
        NotifyInquiryLimitationRequest request = mock(NotifyInquiryLimitationRequest.class);

        ReflectionTestUtils.setField(paymentEventListener, "limitationService", limitationService);

        paymentEventListener.onNotifyPaymentLimitation(request);

        verify(limitationService, times(1)).notifyInquiryLimitation(request);
    }

java.lang.NullPointerException: Cannot invoke "org.springframework.context.ApplicationContext.getBean(java.lang.Class)" because "com.ykb.payments.bill.common.util.SpringUtil.appContext" is null

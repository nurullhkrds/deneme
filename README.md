@Test
void testOnNotifyPaymentLimitation() {
    // Mock the ApplicationContext and LimitationService
    ApplicationContext appContext = mock(ApplicationContext.class);
    LimitationService limitationService = mock(LimitationService.class);

    // Set the appContext in SpringUtil
    ReflectionTestUtils.setField(SpringUtil.class, "appContext", appContext);
    when(appContext.getBean(LimitationService.class)).thenReturn(limitationService);

    // Mock the request
    NotifyPaymentLimitationRequest request = mock(NotifyPaymentLimitationRequest.class);

    // Inject limitationService directly into paymentEventListener for the sake of the test
    ReflectionTestUtils.setField(paymentEventListener, "limitationService", limitationService);

    // Call the method under test
    paymentEventListener.onNotifyPaymentLimitation(request);

    // Verify that the limitationService's method was called
    verify(limitationService, times(1)).notifyPaymentLimitation(request);
}

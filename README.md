@Test
    void testSaveBusinessLog() {
        try (MockedStatic<SpringUtil> mockedSpringUtil = mockStatic(SpringUtil.class)) {
            LoggingService mockLoggingService = mock(LoggingService.class);
            mockedSpringUtil.when(() -> SpringUtil.getBean(LoggingService.class)).thenReturn(mockLoggingService);

            LogUtil.saveBusinessLog(businessLogDTO);

            verify(mockLoggingService, times(1)).saveBusinessLog(businessLogDTO);
        }
    }


org.mockito.exceptions.base.MockitoException: 
The used MockMaker SubclassByteBuddyMockMaker does not support the creation of static mocks

Mockito's inline mock maker supports static mocks based on the Instrumentation API.
You can simply enable this mock mode, by placing the 'mockito-inline' artifact where you are currently using 'mockito-core'.
Note that Mockito's inline mock maker is not supported on Android.

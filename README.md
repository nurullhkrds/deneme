try (MockedStatic<SpringUtil> mockedStatic = Mockito.mockStatic(SpringUtil.class)) {
    DependencyService mockDep = Mockito.mock(DependencyService.class);
    Mockito.when(SpringUtil.getBean(DependencyService.class)).thenReturn(mockDep);
    Mockito.when(mockDep.call()).thenReturn("mocked");

    String result = myService.process();

    assertEquals("mocked", result);
}

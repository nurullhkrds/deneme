@BeforeEach
void setUp() {
    MockitoAnnotations.initMocks(this);

    when(restTemplateBuilder.setConnectTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
    when(restTemplateBuilder.setReadTimeout(any(Duration.class))).thenReturn(restTemplateBuilder);
    when(restTemplateBuilder.build()).thenReturn(restTemplate);

    // Address'i elle set ediyoruz
    ReflectionTestUtils.setField(billPaymentRestFacadeClient, "address", "http://test-address");
    ReflectionTestUtils.setField(billPaymentRestFacadeClient, "readTimeout", 5000L);
    ReflectionTestUtils.setField(billPaymentRestFacadeClient, "connectionTimeout", 5000L);
}

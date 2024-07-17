Mock
    private List<ProvisionService> provisionServices;

    @InjectMocks
    private ProvisionFactory provisionFactory;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetProvisionService() {
        // Hazırlık: Mock provisionServices listesi
        ProvisionService mockProvisionService = new ProvisionService(EnumProvisionType.TYPE1); // EnumProvisionType yerine gerçek bir enum değeri oluşturulmalıdır
        when(provisionServices.stream()).thenReturn(List.of(mockProvisionService).stream());

        // Test: Geçerli bir EnumProvisionType ile getProvisionService çağrısı yapılır
        EnumProvisionType validProvisionType = EnumProvisionType.TYPE1; // Gerçek bir enum değeri atanmalıdır
        ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

        // Assert: Sonuç null olmamalı ve doğru hizmet döndürülmeli
        assertNotNull(result);
        assertEquals(mockProvisionService, result);
    }

    @Test
    void testGetProvisionServiceInvalidType() {
        // Hazırlık: Mock provisionServices listesi
        ProvisionService mockProvisionService = new ProvisionService(EnumProvisionType.TYPE1); // EnumProvisionType yerine gerçek bir enum değeri oluşturulmalıdır
        when(provisionServices.stream()).thenReturn(List.of(mockProvisionService).stream());

        // Test: Geçersiz bir EnumProvisionType ile getProvisionService çağrısı yapılır
        EnumProvisionType invalidProvisionType = EnumProvisionType.INVALID_TYPE; // Geçersiz bir enum değeri atanmalıdır

        // Assert: IllegalArgumentException fırlatılmalı
        assertThrows(IllegalArgumentException.class, () -> provisionFactory.getProvisionService(invalidProvisionType));
    }

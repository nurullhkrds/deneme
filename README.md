  @Mock
    private List<ProvisionService> provisionServices;

    @InjectMocks
    private ProvisionFactory provisionFactory;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);

        // Mock'lanmış ProvisionService nesneleri oluşturuluyor
        ProvisionService provisionService1 = mock(ProvisionService.class);
        ProvisionService provisionService2 = mock(ProvisionService.class);

        // ProvisionService mock'larının getProvisionType metodunu ayarlıyoruz
        when(provisionService1.getProvisionType()).thenReturn(EnumProvisionType.TYPE1);
        when(provisionService2.getProvisionType()).thenReturn(EnumProvisionType.TYPE2);

        // provisionServices listesini mock'lanmış ProvisionService nesneleri ile dolduruyoruz
        provisionServices = List.of(provisionService1, provisionService2);
    }

    @Test
    void whenProvisionFactoryIsInitialized_thenProvisionServiceMapIsCorrectlyPopulated() {
        // ProvisionFactory'i provisionServices listesi ile initialize ediyoruz
        provisionFactory = new ProvisionFactory(provisionServices);

        // ProvisionFactory.getProvisionServiceMap() metodunu çağırarak haritayı alıyoruz
        Map<EnumProvisionType, ProvisionService> provisionServiceMap = ProvisionFactory.getProvisionServiceMap();

        // Haritanın beklenen boyutta olduğunu doğruluyoruz
        assertEquals(2, provisionServiceMap.size());
        // Haritanın beklenen anahtar-değer çiftlerini içerdiğini doğruluyoruz
        assertEquals(provisionServices.get(0), provisionServiceMap.get(EnumProvisionType.TYPE1));
        assertEquals(provisionServices.get(1), provisionServiceMap.get(EnumProvisionType.TYPE2));
    }

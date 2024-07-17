private ProvisionFactory provisionFactory;

    @Mock
    private List<ProvisionService> provisionServices;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
        provisionFactory = new ProvisionFactory(provisionServices);
    }

    @Test
    void testGetProvisionService() {
        // Mock ProvisionService instance
        ProvisionService mockProvisionService = mock(ProvisionService.class);
        EnumProvisionType validProvisionType = EnumProvisionType.CARD;

        // Mock behavior for provisionServices
        when(provisionServices.stream()).thenReturn(List.of(mockProvisionService).stream());
        when(mockProvisionService.getProvisionType()).thenReturn(validProvisionType);

        ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

        assertNotNull(result);
        assertEquals(mockProvisionService, result);
    }

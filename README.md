  @Mock
    private List<ProvisionService> provisionServices;

    @InjectMocks
    private ProvisionFactory provisionFactory;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

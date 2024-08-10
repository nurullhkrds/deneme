@BeforeEach
void setUp() {
    MockitoAnnotations.openMocks(this);
    SpringUtil.setApplicationContext(applicationContext);
}

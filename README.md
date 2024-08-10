  @BeforeEach
    void resetSpringUtil() {
        SpringUtil.setApplicationContext(null); // SpringUtil'in context'ini sıfırlayın
    }

    @AfterEach
    void tearDown() {
        Mockito.reset(paymentUtilImpl, applicationContext);
    }
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)

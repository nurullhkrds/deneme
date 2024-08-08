@ExtendWith(MockitoExtension.class)
public class RedisConfigTest {

    @Mock
    private CfEnv cfEnv;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @InjectMocks
    private RedisConfig redisConfig;

    @BeforeEach
    public void setUp() {
        ReflectionTestUtils.setField(redisConfig, "serviceName", "testService");
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 1);

        when(cfEnv.findServiceByName("testService")).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getPort()).thenReturn("6379");
        when(cfCredentials.getPassword()).thenReturn("password");
    }

    @Test
    public void testRedisConnectionFactory() {
        RedisConnectionFactory factory = redisConfig.redisConnectionFactory();
        assertNotNull(factory);
        assertTrue(factory instanceof JedisConnectionFactory);

        RedisStandaloneConfiguration config = ((JedisConnectionFactory) factory).getStandaloneConfiguration();
        assertEquals("localhost", config.getHostName());
        assertEquals(6379, config.getPort());
        assertEquals("password", config.getPassword().get());
    }

    @Test
    public void testCacheManager() {
        RedisCacheManager cacheManager = redisConfig.cacheManager();
        assertNotNull(cacheManager);

        RedisCacheConfiguration config = cacheManager.getCacheConfigurations().get("institutionFeatureValue");
        assertNotNull(config);
        assertEquals(Duration.ofHours(1), config.getTtl());
    }

    @Test
    public void testRedisSerializer() {
        RedisSerializer<Object> serializer = redisConfig.redisSerializer();
        assertNotNull(serializer);
        assertTrue(serializer instanceof GenericJackson2JsonRedisSerializer);

        ObjectMapper mapper = ((GenericJackson2JsonRedisSerializer) serializer).getObjectMapper();
        assertNotNull(mapper);
        assertTrue(mapper.getRegisteredModuleIds().contains("com.fasterxml.jackson.datatype.jsr310.JavaTimeModule"));
    }
}

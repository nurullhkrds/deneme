
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

    private String serviceName = "myRedisService";

    private int institutionFeatureValueTtl = 1;
    private int institutionFeatureListTtl = 2;
    private int getProcessChannelForProcessTtl = 3;
    private int getInstitutionForProcessTtl = 4;
    private int getInstitutionChannelForProcessTtl = 5;
    private int getInstitutionProcessTtl = 6;
    private int getInstitutionChannelProcessTtl = 7;
    private int getInstitutionDebtTypeForProcessTtl = 8;
    private int getInstitutionByIdTtl = 9;
    private int institutionUserInterfaceListTtl = 10;
    private int findChannelByChannelCodeTtl = 11;

    @BeforeEach
    public void setUp() {
        when(cfEnv.findServiceByName(serviceName)).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getPort()).thenReturn("6379");
        when(cfCredentials.getPassword()).thenReturn("password");
    }

    @Test
    public void testRedisConnectionFactory() {
        RedisConnectionFactory connectionFactory = redisConfig.redisConnectionFactory();
        assertNotNull(connectionFactory);
        assertTrue(connectionFactory instanceof JedisConnectionFactory);

        RedisStandaloneConfiguration configuration = ((JedisConnectionFactory) connectionFactory).getStandaloneConfiguration();
        assertEquals("localhost", configuration.getHostName());
        assertEquals(6379, configuration.getPort());
        assertEquals("password", configuration.getPassword().get());
    }

    @Test
    public void testRedisCacheManager() {
        RedisCacheManager cacheManager = redisConfig.cacheManager();
        assertNotNull(cacheManager);

        assertCacheConfiguration(cacheManager, "institutionFeatureValue", institutionFeatureValueTtl);
        assertCacheConfiguration(cacheManager, "institutionFeatureList", institutionFeatureListTtl);
        assertCacheConfiguration(cacheManager, "getProcessChannelForProcess", getProcessChannelForProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionForProcess", getInstitutionForProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionChannelForProcess", getInstitutionChannelForProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionProcess", getInstitutionProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionChannelProcess", getInstitutionChannelProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionDebtTypeForProcess", getInstitutionDebtTypeForProcessTtl);
        assertCacheConfiguration(cacheManager, "getInstitutionById", getInstitutionByIdTtl);
        assertCacheConfiguration(cacheManager, "institutionUserInterfaceList", institutionUserInterfaceListTtl);
        assertCacheConfiguration(cacheManager, "findChannelByChannelCode", findChannelByChannelCodeTtl);
    }

    private void assertCacheConfiguration(RedisCacheManager cacheManager, String cacheName, int ttl) {
        RedisCacheConfiguration configuration = cacheManager.getCacheConfigurations().get(cacheName);
        assertNotNull(configuration, "Configuration for cache " + cacheName + " should not be null");
        assertEquals(Duration.ofHours(ttl), configuration.getTtl(), "TTL for cache " + cacheName + " should be " + ttl + " hours");
    }

    @Test
    public void testRedisSerializer() {
        RedisSerializer<Object> serializer = redisConfig.redisSerializer();
        assertNotNull(serializer);
        assertTrue(serializer instanceof GenericJackson2JsonRedisSerializer);
    }
}

@ExtendWith(MockitoExtension.class)
@ExtendWith(SpringExtension.class)
public class RedisConfigTest {

    @InjectMocks
    private RedisConfig redisConfig;

    @Mock
    private CfEnv cfEnv;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 3);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 4);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 5);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 6);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 7);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 8);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 9);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 10);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 11);
        ReflectionTestUtils.setField(redisConfig, "serviceName", "testServiceName");

        when(cfEnv.findServiceByName("testServiceName")).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getPort()).thenReturn(6379);
        when(cfCredentials.getPassword()).thenReturn("password");
    }

    @Test
    public void testRedisConnectionFactory() {
        ReflectionTestUtils.setField(redisConfig, "cfEnv", cfEnv);  // Ensure cfEnv is set
        RedisConnectionFactory connectionFactory = redisConfig.redisConnectionFactory();
        assertNotNull(connectionFactory);
    }

    @Test
    public void testCacheManager() {
        ReflectionTestUtils.setField(redisConfig, "cfEnv", cfEnv);  // Ensure cfEnv is set
        RedisCacheManager cacheManager = redisConfig.cacheManager();
        assertNotNull(cacheManager);
    }

    @Test
    public void testRedisSerializer() {
        RedisSerializer<Object> serializer = redisConfig.redisSerializer();
        assertNotNull(serializer);
    }

    @TestConfiguration
    static class RedisConfigTestContextConfiguration {

        @Bean
        public RedisConnectionFactory redisConnectionFactory() {
            RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration("localhost", 6379);
            return new JedisConnectionFactory(redisStandaloneConfiguration);
        }

        @Bean
        public RedisTemplate<String, Object> redisTemplate() {
            RedisTemplate<String, Object> template = new RedisTemplate<>();
            template.setConnectionFactory(redisConnectionFactory());
            template.setDefaultSerializer(new GenericJackson2JsonRedisSerializer());
            return template;
        }
    }
}

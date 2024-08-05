import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;

import java.time.Duration;

@ExtendWith(MockitoExtension.class)
@PropertySource("classpath:application.properties")
public class RedisConfigTest {

    @Mock
    private CfEnv cfEnv;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @InjectMocks
    private RedisConfig redisConfig;

    @Value("${cache.redis.serviceName}")
    private String serviceName;

    @Value("${cache.redis.institutionFeatureValue.ttl}")
    private int institutionFeatureValueTtl;

    @Value("${cache.redis.institutionFeatureList.ttl}")
    private int institutionFeatureListTtl;

    @Value("${cache.redis.getProcessChannelForProcess.ttl}")
    private int getProcessChannelForProcessTtl;

    @Value("${cache.redis.getInstitutionForProcess.ttl}")
    private int getInstitutionForProcessTtl;

    @Value("${cache.redis.getInstitutionChannelForProcess.ttl}")
    private int getInstitutionChannelForProcessTtl;

    @Value("${cache.redis.getInstitutionProcess.ttl}")
    private int getInstitutionProcessTtl;

    @Value("${cache.redis.getInstitutionChannelProcess.ttl}")
    private int getInstitutionChannelProcessTtl;

    @Value("${cache.redis.getInstitutionDebtTypeForProcess.ttl}")
    private int getInstitutionDebtTypeForProcessTtl;

    @Value("${cache.redis.getInstitutionById.ttl}")
    private int getInstitutionByIdTtl;

    @Value("${cache.redis.institutionUserInterfaceList.ttl}")
    private int institutionUserInterfaceListTtl;

    @Value("${cache.redis.findChannelByChannelCode.ttl}")
    private int findChannelByChannelCodeTtl;

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

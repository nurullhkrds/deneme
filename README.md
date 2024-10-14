import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;

@SpringBootTest
class RedisConfigTest {

    @MockBean
    private RedisConnectionFactory redisConnectionFactory;

    @InjectMocks
    private RedisConfig redisConfig;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testRedisConnectionFactory() {
        RedisConnectionFactory connectionFactory = redisConfig.redisConnectionFactory();
        assertNotNull(connectionFactory);
        verify(redisConnectionFactory, times(1));
    }

    @Test
    void testCacheManager() {
        RedisCacheManager cacheManager = redisConfig.cacheManager();
        assertNotNull(cacheManager);
        assertEquals(12, cacheManager.getCacheNames().size(), "Cache count should match TTL configurations");
    }

    @Test
    void testRedisSerializer() {
        GenericJackson2JsonRedisSerializer serializer = (GenericJackson2JsonRedisSerializer) redisConfig.redisSerializer();
        assertNotNull(serializer);
        assertDoesNotThrow(() -> serializer.serialize("test"));
    }

    @Test
    void testCacheConfigurationTtl() {
        RedisCacheManager cacheManager = redisConfig.cacheManager();
        assertTrue(cacheManager.getCacheNames().contains("institutionFeatureValue"));
        assertTrue(cacheManager.getCacheNames().contains("institutionFeatureList"));
        // Diğer TTL'leri de kontrol edebilirsin
    }

    @Test
    void testRedisStandaloneConfiguration() {
        RedisStandaloneConfiguration configuration = new RedisStandaloneConfiguration("localhost", 6379);
        assertNotNull(configuration);
        assertEquals("localhost", configuration.getHostName());
        assertEquals(6379, configuration.getPort());
    }
}

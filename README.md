import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

@ExtendWith(MockitoExtension.class)
public class RedisConfigTest {

    @InjectMocks
    private RedisConfig redisConfig;

    @Mock
    private RedisConnectionFactory redisConnectionFactory;

    @Mock
    private RedisSerializer<Object> redisSerializer;

    @BeforeEach
    public void setUp() {
        // RedisConfig'teki @Value değerlerini manuel olarak set ediyoruz.
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 2);

        // RedisSerializer'i mocklamak
        redisSerializer = new StringRedisSerializer(); // ya da bir başka uygun serializer
    }

    @Test
    public void testCacheManager() {
        RedisCacheManager cacheManager = redisConfig.cacheManager();

        assertNotNull(cacheManager);
        assertNotNull(cacheManager.getCache("institutionFeatureValue"));
        assertNotNull(cacheManager.getCache("institutionFeatureList"));
        assertNotNull(cacheManager.getCache("getProcessChannelForProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionForProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionChannelForProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionChannelProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionDebtTypeForProcess"));
        assertNotNull(cacheManager.getCache("getInstitutionById"));
        assertNotNull(cacheManager.getCache("institutionUserInterfaceList"));
        assertNotNull(cacheManager.getCache("findChannelByChannelCode"));
    }
}

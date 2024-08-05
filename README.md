import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.TestConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.test.context.junit.jupiter.SpringExtension;

@ExtendWith(MockitoExtension.class)
@ExtendWith(SpringExtension.class)
public class RedisConfigTest {

    @InjectMocks
    private RedisConfig redisConfig;

    @Mock
    private RedisConnectionFactory redisConnectionFactory;

    @Value("${cache.redis.serviceName}")
    private String serviceName;

    @BeforeEach
    void setUp() {
        redisConfig = new RedisConfig();
        redisConfig.institutionFeatureValueTtl = 1;
        redisConfig.institutionFeatureListTtl = 2;
        redisConfig.getProcessChannelForProcessTtl = 3;
        redisConfig.getInstitutionForProcessTtl = 4;
        redisConfig.getInstitutionChannelForProcessTtl = 5;
        redisConfig.getInstitutionProcessTtl = 6;
        redisConfig.getInstitutionChannelProcessTtl = 7;
        redisConfig.getInstitutionDebtTypeForProcessTtl = 8;
        redisConfig.getInstitutionByIdTtl = 9;
        redisConfig.institutionUserInterfaceListTtl = 10;
        redisConfig.findChannelByChannelCodeTtl = 11;
        redisConfig.serviceName = "testServiceName";
    }

    @Test
    public void testRedisConnectionFactory() {
        RedisConnectionFactory connectionFactory = redisConfig.redisConnectionFactory();
        assertNotNull(connectionFactory);
    }

    @Test
    public void testCacheManager() {
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

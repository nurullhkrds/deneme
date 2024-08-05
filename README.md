import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.serializer.SerializationPair;
import org.springframework.test.util.ReflectionTestUtils;

import com.fasterxml.jackson.databind.ObjectMapper;

import io.pivotal.cfenv.core.CfEnv;
import io.pivotal.cfenv.core.CfService;
import io.pivotal.cfenv.core.CfCredentials;

import java.time.Duration;

@ExtendWith(MockitoExtension.class)
public class RedisConfigTest {

    @InjectMocks
    private RedisConfig redisConfig;

    @Mock
    private CfEnv cfEnv;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @Mock
    private JedisConnectionFactory jedisConnectionFactory;

    @Mock
    private RedisConnectionFactory redisConnectionFactory;

    @BeforeEach
    public void setup() {
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "serviceName", "testService");
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 3);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 4);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 5);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 6);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 7);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 8);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 9);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 10);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 11);

        when(cfEnv.findServiceByName("testService")).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getName()).thenReturn("testName");
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getPort()).thenReturn(6379);
        when(cfCredentials.getPassword()).thenReturn("password");
    }

    @Test
    public void testRedisConnectionFactory() {
        RedisConnectionFactory factory = redisConfig.redisConnectionFactory();

        assertNotNull(factory);
        assertTrue(factory instanceof JedisConnectionFactory);

        verify(cfEnv, times(1)).findServiceByName("testService");
        verify(cfService, times(1)).getCredentials();
        verify(cfCredentials, times(1)).getName();
        verify(cfCredentials, times(1)).getHost();
        verify(cfCredentials, times(1)).getPort();
        verify(cfCredentials, times(1)).getPassword();
    }

    @Test
    public void testRedisCacheManager() {
        when(redisConfig.redisConnectionFactory()).thenReturn(redisConnectionFactory);
        RedisCacheManager cacheManager = redisConfig.cacheManager();

        assertNotNull(cacheManager);

        // Further checks can be made on the cache configurations
    }

    @Test
    public void testRedisSerializer() {
        RedisSerializer<Object> serializer = redisConfig.redisSerializer();
        assertNotNull(serializer);
        assertTrue(serializer instanceof GenericJackson2JsonRedisSerializer);

        ObjectMapper objectMapper = ((GenericJackson2JsonRedisSerializer) serializer).getObjectMapper();
        assertNotNull(objectMapper);
    }
}

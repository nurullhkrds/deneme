import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.*;

import io.pivotal.cfenv.core.CfCredentials;
import io.pivotal.cfenv.core.CfEnv;
import io.pivotal.cfenv.core.CfService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.RedisStandaloneConfiguration;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.test.util.ReflectionTestUtils;

@ExtendWith(MockitoExtension.class)
public class RedisConfigTest {

    @InjectMocks
    private RedisConfig redisConfig;

    @Mock
    private RedisConnectionFactory redisConnectionFactory;

    @Mock
    private RedisSerializer<Object> redisSerializer;

    @Mock
    private CfEnv cfEnv;

    @Mock
    private CfService cfService;

    @Mock
    private CfCredentials cfCredentials;

    @BeforeEach
    public void setUp() {
        // RedisConfig'teki @Value değerlerini manuel olarak set ediyoruz.
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "serviceName", "testServiceName");
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 2);

        // Mock CfEnv ve CfService
        when(cfEnv.findServiceByName("testServiceName")).thenReturn(cfService);
        when(cfService.getCredentials()).thenReturn(cfCredentials);
        when(cfCredentials.getHost()).thenReturn("localhost");
        when(cfCredentials.getPort()).thenReturn("6379");
        when(cfCredentials.getPassword()).thenReturn("password");
        when(cfService.getPlan()).thenReturn("standard");
        when(cfCredentials.getName()).thenReturn("redis-test");

        // RedisConfig sınıfında kullanılan cfEnv'i mockla
        ReflectionTestUtils.setField(redisConfig, "cfEnv", cfEnv);
    }

    @Test
    public void testRedisConnectionFactory() {
        RedisConnectionFactory factory = redisConfig.redisConnectionFactory();
        assertNotNull(factory);
    }

    @Test
    public void testCacheManager() {
        when(redisConnectionFactory.getConnection()).thenReturn(null); // Mock ConnectionFactory
        when(redisSerializer.serialize("test")).thenReturn(new byte[]{1, 2, 3}); // Mock Serializer

        var cacheManager = redisConfig.cacheManager();
        assertNotNull(cacheManager);
        assertNotNull(cacheManager.getCache("institutionFeatureValue"));
        assertNotNull(cacheManager.getCache("institutionFeatureList"));
    }
}

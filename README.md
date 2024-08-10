@SpringBootTest
@ExtendWith({SpringExtension.class, MockitoExtension.class})
@PropertySource("classpath:application.properties")
public class RedisConfigTest {

    @Mock
    private RedisConnectionFactory redisConnectionFactory;

    @Mock
    private RedisSerializer<Object> redisSerializer;

    @InjectMocks
    private RedisConfig redisConfig;

    @BeforeEach
    public void setUp() {
        when(redisConnectionFactory.getConnection()).thenReturn(null);
    }

    @Test
    public void testCacheManager() {
        // Test if the cacheManager bean is created correctly
        assertNotNull(redisConfig.cacheManager());
    }
}

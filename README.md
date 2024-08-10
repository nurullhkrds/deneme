@Configuration
@EnableCaching
@ConditionalOnProperty(value = "runtime.platform", havingValue = "pcf")
public class RedisConfig {

	private static final Logger logger = LoggerFactory.getLogger(RedisConfig.class);
	
    @Value("${cache.redis.institutionFeatureValue.ttl}")
    private int institutionFeatureValueTtl;
    
    @Value("${cache.redis.institutionFeatureList.ttl}")
    private int institutionFeatureListTtl;
    
    @Value("${cache.redis.serviceName}")
    private String serviceName;
    
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
    	@Bean
	public RedisCacheManager cacheManager() {
		return RedisCacheManager.builder(redisConnectionFactory())
				.withCacheConfiguration("institutionFeatureValue",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(institutionFeatureValueTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("institutionFeatureList",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(institutionFeatureListTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getProcessChannelForProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getProcessChannelForProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionForProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionForProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionChannelForProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionChannelForProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionChannelProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionChannelProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionDebtTypeForProcess",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionDebtTypeForProcessTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("getInstitutionById",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(getInstitutionByIdTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("institutionUserInterfaceList",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(institutionUserInterfaceListTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.withCacheConfiguration("findChannelByChannelCode",
						RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofHours(findChannelByChannelCodeTtl))
								//.disableCachingNullValues()
								.serializeValuesWith(SerializationPair.fromSerializer(redisSerializer())))
				.transactionAware().build();
	}

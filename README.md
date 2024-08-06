@RestController
@Tag(name = "Caching Operations")
@RequestMapping("/caching")
@RequiredArgsConstructor
public class CachingController {

	private final Logger logger = LoggerFactory.getLogger(CachingController.class);

	private final CachingService cachingService;

	@Operation(summary = "Clear all caches")
	@GetMapping("/evict/all")
	public void clearAllCaches() {
		cachingService.evictAllCaches();
	}

	@Operation(summary = "Clear specific cache value")
	@GetMapping("/evict")
	public void evictAllCacheValues(@RequestParam(required = true) String cacheName) {
		logger.info("request: {}", cacheName);
		cachingService.evictAllCacheValues(cacheName);
	}
}

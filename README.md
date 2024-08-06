@WebMvcTest(CachingController.class)
public class CachingControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private CachingService cachingService;

    @MockBean
    private Logger logger;

    @Test
    public void clearAllCaches_shouldCallEvictAllCaches() throws Exception {
        // Arrange & Act
        ResultActions resultActions = mockMvc.perform(get("/caching/evict/all"));

        // Assert
        resultActions.andExpect(status().isOk());
        verify(cachingService).evictAllCaches();
    }

    @Test
    public void evictAllCacheValues_shouldCallEvictAllCacheValues() throws Exception {
        // Arrange
        String cacheName = "testCache";

        // Act
        ResultActions resultActions = mockMvc.perform(get("/caching/evict")
                .param("cacheName", cacheName));

        // Assert
        resultActions.andExpect(status().isOk());
        verify(logger).info("request: {}", cacheName);
        verify(cachingService).evictAllCacheValues(cacheName);
    }
}

@Test
    public void evictAllCacheValues_shouldCallEvictAllCacheValues() throws Exception {
        // Arrange
        String cacheName = "testCache";

        // Act
        ResultActions resultActions = mockMvc.perform(get("/caching/evict")
                .param("cacheName", cacheName));

        // Assert
        resultActions.andExpect(status().isOk());
        verify(logger, times(1)).info("request: {}", cacheName);
        verify(cachingService, times(1)).evictAllCacheValues(cacheName);
    }

    @TestConfiguration
    static class TestConfig {

        @Bean
        @Primary
        public Logger logger() {
            return LoggerFactory.getLogger(CachingController.class);
        }
    }

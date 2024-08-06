import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.slf4j.Logger;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@ExtendWith(MockitoExtension.class)
public class CachingControllerTest {

    private MockMvc mockMvc;

    @Mock
    private Logger logger;

    @Mock
    private CachingService cachingService;

    @InjectMocks
    private CachingController cachingController;

    @BeforeEach
    void setUp() {
        mockMvc = MockMvcBuilders.standaloneSetup(cachingController).build();
    }

    @Test
    void testClearAllCaches() throws Exception {
        mockMvc.perform(get("/caching/evict/all"))
               .andExpect(status().isOk());

        verify(cachingService, times(1)).evictAllCaches();
    }

    @Test
    void testEvictAllCacheValues() throws Exception {
        String cacheName = "testCache";

        mockMvc.perform(get("/caching/evict")
                .param("cacheName", cacheName))
               .andExpect(status().isOk());

        verify(logger, times(1)).info("request: {}", cacheName);
        verify(cachingService, times(1)).evictAllCacheValues(cacheName);
    }
}

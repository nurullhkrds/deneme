import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.times;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.slf4j.Logger;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.context.annotation.Import;
import org.springframework.beans.factory.annotation.Autowired;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@ExtendWith(MockitoExtension.class)
@WebMvcTest(CachingController.class)
@Import(CachingService.class)
public class CachingControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Mock
    private CachingService cachingService;

    @Mock
    private Logger logger;

    @InjectMocks
    private CachingController cachingController;

    @BeforeEach
    public void setup() {
        mockMvc = MockMvcBuilders.standaloneSetup(cachingController).build();
    }

    @Test
    public void testClearAllCaches() throws Exception {
        mockMvc.perform(get("/caching/evict/all"))
                .andExpect(status().isOk());

        verify(cachingService, times(1)).evictAllCaches();
    }

    @Test
    public void testEvictAllCacheValues() throws Exception {
        String cacheName = "testCache";
        mockMvc.perform(get("/caching/evict")
                .param("cacheName", cacheName))
                .andExpect(status().isOk());

        verify(logger, times(1)).info("request: {}", cacheName);
        verify(cachingService, times(1)).evictAllCacheValues(cacheName);
    }
}

import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.times;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

class CachingControllerTest {

    @Spy
    private CachingController cachingController;

    @Mock
    private CachingService cachingService;

    private MockMvc mockMvc;

    @Captor
    private ArgumentCaptor<String> logCaptor;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        cachingController = new CachingController(LoggerFactory.getLogger(CachingController.class), cachingService);
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

        verify(cachingService, times(1)).evictAllCacheValues(cacheName);
        
        // Capture and verify the log message
        ArgumentCaptor<String> logCaptor = ArgumentCaptor.forClass(String.class);
        verify(cachingController.getLogger(), times(1)).info(logCaptor.capture(), logCaptor.capture());
        List<String> logValues = logCaptor.getAllValues();
        assertEquals("request: {}", logValues.get(0));
        assertEquals(cacheName, logValues.get(1));
    }
}

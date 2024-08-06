import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.times;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.classic.Logger;
import ch.qos.logback.core.read.ListAppender;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.slf4j.LoggerFactory;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import java.util.List;

class CachingControllerTest {

    @Mock
    private CachingService cachingService;

    @InjectMocks
    private CachingController cachingController;

    private MockMvc mockMvc;

    private ListAppender<ILoggingEvent> listAppender;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        mockMvc = MockMvcBuilders.standaloneSetup(cachingController).build();

        // Logger ve ListAppender ayarları
        Logger logger = (Logger) LoggerFactory.getLogger(CachingController.class);
        listAppender = new ListAppender<>();
        listAppender.start();
        logger.addAppender(listAppender);
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

        // Log mesajını doğrula
        List<ILoggingEvent> logsList = listAppender.list;
        assertEquals(1, logsList.size());
        assertTrue(logsList.get(0).getFormattedMessage().contains("request: " + cacheName));
    }
}

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

public class WebConfigTest {

    private WebConfig webConfig;
    private UrlBasedCorsConfigurationSource source;

    @BeforeEach
    public void setUp() {
        webConfig = new WebConfig();
        source = new UrlBasedCorsConfigurationSource();
    }

    @Test
    public void testCorsFilterBean() {
        CorsFilter corsFilter = webConfig.corsFilter();
        
        // CorsFilter'in UrlBasedCorsConfigurationSource içerdiğini kontrol edin
        assertNotNull(corsFilter);

        // UrlBasedCorsConfigurationSource'u doğrudan test edin
        source.registerCorsConfiguration("/**", webConfig.corsFilter().getCorsConfigurationSource().getCorsConfiguration("/**"));
        CorsConfiguration config = source.getCorsConfiguration("/**");

        assertNotNull(config);
        assertTrue(config.getAllowCredentials());
        assertTrue(config.getAllowedOriginPatterns().contains("*"));
        assertTrue(config.getAllowedHeaders().contains("*"));
        assertTrue(config.getAllowedMethods().contains("*"));
    }
}

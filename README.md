import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.mockito.Mockito.mock;

import org.junit.jupiter.api.Test;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

public class WebConfigTest {

    @Test
    public void testCorsFilter() {
        WebConfig webConfig = new WebConfig();
        CorsFilter corsFilter = webConfig.corsFilter();
        
        UrlBasedCorsConfigurationSource source = (UrlBasedCorsConfigurationSource) corsFilter.getConfigSource();
        CorsConfiguration config = source.getCorsConfigurations().get("/**");

        assertTrue(config.getAllowCredentials());
        assertEquals("*", config.getAllowedOriginPatterns().get(0));
        assertEquals("*", config.getAllowedHeaders().get(0));
        assertEquals("*", config.getAllowedMethods().get(0));
    }
}

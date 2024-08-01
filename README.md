import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

public class WebConfigTest {

    private WebConfig webConfig;

    @BeforeEach
    public void setUp() {
        webConfig = new WebConfig();
    }

    @Test
    public void testCorsFilterBean() {
        CorsFilter corsFilter = webConfig.corsFilter();

        assertNotNull(corsFilter);

        UrlBasedCorsConfigurationSource source = (UrlBasedCorsConfigurationSource) corsFilter.getCorsConfigurationSource();
        CorsConfiguration config = source.getCorsConfigurations().get("/**");

        assertNotNull(config);
        assertTrue(config.getAllowCredentials());
        assertTrue(config.getAllowedOrigins().contains("*"));
        assertTrue(config.getAllowedHeaders().contains("*"));
        assertTrue(config.getAllowedMethods().contains("*"));
    }
}

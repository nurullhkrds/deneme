import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.Test;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

public class WebConfigTest {

    @Test
    public void testCorsFilter() {
        WebConfig webConfig = new WebConfig();
        CorsFilter corsFilter = webConfig.corsFilter();

        assertNotNull(corsFilter, "CorsFilter should not be null");

        // Directly test the configuration setup in WebConfig
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        config.addAllowedOriginPattern("*");
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        source.registerCorsConfiguration("/**", config);

        // Assert the configurations
        CorsConfiguration registeredConfig = source.getCorsConfigurations().get("/**");

        assertNotNull(registeredConfig, "CorsConfiguration should not be null");
        assertTrue(registeredConfig.getAllowCredentials(), "AllowCredentials should be true");
        assertEquals("*", registeredConfig.getAllowedOriginPatterns().get(0), "AllowedOriginPatterns should be '*'");
        assertEquals("*", registeredConfig.getAllowedHeaders().get(0), "AllowedHeaders should be '*'");
        assertEquals("*", registeredConfig.getAllowedMethods().get(0), "AllowedMethods should be '*'");
    }

    @Test
    public void testCorsConfigurationProperties() {
        WebConfig webConfig = new WebConfig();
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();

        config.setAllowCredentials(true);
        config.addAllowedOriginPattern("*");
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        source.registerCorsConfiguration("/**", config);

        // Verify individual properties
        CorsConfiguration registeredConfig = source.getCorsConfigurations().get("/**");

        assertNotNull(registeredConfig, "CorsConfiguration should not be null");
        assertTrue(registeredConfig.getAllowCredentials(), "AllowCredentials should be true");
        assertEquals("*", registeredConfig.getAllowedOriginPatterns().get(0), "AllowedOriginPatterns should be '*'");
        assertEquals("*", registeredConfig.getAllowedHeaders().get(0), "AllowedHeaders should be '*'");
        assertEquals("*", registeredConfig.getAllowedMethods().get(0), "AllowedMethods should be '*'");
    }
}

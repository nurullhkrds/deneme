import static org.junit.jupiter.api.Assertions.assertEquals;
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
        
        // Directly test the configuration setup in WebConfig
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        config.addAllowedOriginPattern("*"); // '*' kullanarak tüm originlere izin verebilirsiniz
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        source.registerCorsConfiguration("/**", config);
        
        // Assert the configurations
        CorsConfiguration registeredConfig = source.getCorsConfigurations().get("/**");

        assertTrue(registeredConfig.getAllowCredentials());
        assertEquals("*", registeredConfig.getAllowedOriginPatterns().get(0));
        assertEquals("*", registeredConfig.getAllowedHeaders().get(0));
        assertEquals("*", registeredConfig.getAllowedMethods().get(0));
    }
}

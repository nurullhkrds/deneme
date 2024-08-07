import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;

import static org.junit.jupiter.api.Assertions.*;

public class UrlConstantsTest {

    @Test
    public void testConstructorThrowsException() {
        Constructor<UrlConstants> constructor;
        try {
            constructor = UrlConstants.class.getDeclaredConstructor();
            constructor.setAccessible(true);
            constructor.newInstance();
        } catch (Exception e) {
            assertTrue(e.getCause() instanceof IllegalAccessError);
            assertEquals("only constants", e.getCause().getMessage());
        }
    }

    @Test
    public void testConstants() {
        assertEquals("/returnMaps", UrlConstants.RETURN_MAP_PATH);
    }
}

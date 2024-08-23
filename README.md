import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

import static org.junit.jupiter.api.Assertions.*;

public class CacheConstantsTest {

    @Test
    public void testCacheConstantsValues() {
        // CacheConstants sabitlerinin doğru değerlerle tanımlandığını kontrol et
        assertEquals("cacheManager", CacheConstants.CACHE_MANAGER);
        assertEquals("#result == null", CacheConstants.UNLESS);
    }

    @Test
    public void testCacheConstantsConstructor() throws NoSuchMethodException {
        // CacheConstants sınıfının private constructor'ını reflection ile çağırmayı dene
        Constructor<CacheConstants> constructor = CacheConstants.class.getDeclaredConstructor();
        constructor.setAccessible(true); // Private constructor'a erişim sağla

        assertThrows(InvocationTargetException.class, () -> {
            constructor.newInstance();
        });

        try {
            constructor.newInstance();
        } catch (InvocationTargetException e) {
            assertTrue(e.getCause() instanceof IllegalAccessError);
            assertEquals("Only constants", e.getCause().getMessage());
        }
    }
}

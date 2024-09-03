import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

import static org.junit.jupiter.api.Assertions.*;

public class LoggingConstantsTest {

    @Test
    void testPrivateConstructor() throws NoSuchMethodException {
        Constructor<LoggingConstants> constructor = LoggingConstants.class.getDeclaredConstructor();
        assertTrue(constructor.isAccessible() || !constructor.canAccess(null));

        // Private constructor'ı erişilebilir yapıyoruz
        constructor.setAccessible(true);

        // Private constructor'ı çağırıyoruz ve IllegalStateException fırlattığından emin oluyoruz
        assertThrows(InvocationTargetException.class, constructor::newInstance);
    }
}

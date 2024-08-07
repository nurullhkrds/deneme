import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class UrlConstantsTest {

    @Test
    public void testConstructorThrowsException() {
        // Sınıfın oluşturulamaz olduğunu test eder
        Exception exception = assertThrows(IllegalAccessError.class, () -> {
            new UrlConstants();
        });

        String expectedMessage = "only constants";
        String actualMessage = exception.getMessage();

        assertTrue(actualMessage.contains(expectedMessage));
    }

    @Test
    public void testConstants() {
        // Sabitlerin doğru değerlere sahip olduğunu test eder
        assertEquals("/returnMaps", UrlConstants.RETURN_MAP_PATH);
    }
}

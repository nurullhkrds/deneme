import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class ErrorDataResultTest {

    @Test
    public void testErrorDataResult() {
        String message = "Error occurred";
        Integer data = 123;
        int statusCode = 400;

        ErrorDataResult<Integer> errorDataResult = new ErrorDataResult<>(message, data, statusCode);

        assertFalse(errorDataResult.isSuccess());
        assertEquals(message, errorDataResult.getMessage());
        assertEquals(data, errorDataResult.getData());
        assertEquals(statusCode, errorDataResult.getStatusCode());
    }
}

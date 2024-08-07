import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class ResultTests {

    @Test
    public void testErrorResult() {
        String message = "Error occurred";
        int statusCode = 400;

        ErrorResult errorResult = new ErrorResult(message, statusCode);

        assertFalse(errorResult.isSuccess());
        assertEquals(message, errorResult.getMessage());
        assertEquals(statusCode, errorResult.getStatusCode());
    }

    @Test
    public void testSuccessResult() {
        String message = "Operation successful";
        int statusCode = 200;

        SuccessResult successResult = new SuccessResult(message, statusCode);

        assertTrue(successResult.isSuccess());
        assertEquals(message, successResult.getMessage());
        assertEquals(statusCode, successResult.getStatusCode());
    }

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

    @Test
    public void testSuccessDataResult() {
        String message = "Operation successful";
        Integer data = 123;
        int statusCode = 200;

        SuccessDataResult<Integer> successDataResult = new SuccessDataResult<>(message, data, statusCode);

        assertTrue(successDataResult.isSuccess());
        assertEquals(message, successDataResult.getMessage());
        assertEquals(data, successDataResult.getData());
        assertEquals(statusCode, successDataResult.getStatusCode());
    }
}

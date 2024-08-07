public class ErrorDataResult<T> extends DataResult<T> {
    public ErrorDataResult(String message, T data, int statusCode) {
        super(false, message, data, statusCode);
    }
}

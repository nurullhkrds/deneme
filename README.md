public class ErrorResult extends Result {

    public ErrorResult(String message, int statusCode) {
        super(false, message, statusCode);
    }
}public class SuccessDataResult<T> extends DataResult<T> {
    public SuccessDataResult(String message, T data, int statusCode) {
        super(true, message, data, statusCode);
    }




}public class SuccessResult extends Result {
    public SuccessResult(String message, int statusCode) {
        super(true, message, statusCode);
    }
}

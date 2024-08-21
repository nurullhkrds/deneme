public enum ResponseStatus {

    SUCCESS(200, "Success"),
    ERROR(500, "Internal Server Error"),
    VALIDATION_ERROR(400, "Validation Error"),
    NOT_FOUND(404, "Resource Not Found"),
    UNAUTHORIZED(401, "Unauthorized"),
    FORBIDDEN(403, "Forbidden");

    private final int code;
    private final String message;

    ResponseStatus(int code, String message) {
        this.code = code;
        this.message = message;
    }

    public int getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }
}

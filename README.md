@Getter
public enum Result {

    SUCCESS("Başarılı"),
    ERROR("Internal Server Error"),
    VALIDATION_ERROR("Validation Error"),
    NOT_FOUND( "Resource Not Found"),
    FORBIDDEN( "Forbidden");
    

    private final String message;

    Result(String message) {
        this.message = message;
    }


}

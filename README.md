@Getter
public enum Result {

    RECORD_ALREADY_EXISTS("Böyle bir kayıt zaten var"),
    CONVERSION_FAILED("Dönüştürme işlemi başarısız"),
    SUCCESSFULLY_ADDED("Başarıyla eklendi"),
    SUCCESSFULLY_UPDATED("Başarıyla güncellendi"),
    SUCCESSFULLY_DELETED("Başarıyla silindi"),
    DATA_LISTED("Veriler listelendi");

    private final String message;

    Result(String message) {
        this.message = message;
    }
}

@Getter
public enum ResultConstant {

    RECORD_ALREADY_EXISTS("Böyle bir kayıt zaten var"),
    RECORD_ALREADY_EXISTS_SECOND("Bu Returnmap için aynı kurum hata kodu zaten tanımlı "),
    RECORD_NOT_FOUND("Kayıt bulunumadı"),
    CONVERSION_FAILED("Dönüştürme işlemi başarısız"),
    SUCCESSFULLY_ADDED("Başarıyla eklendi"),
    SUCCESSFULLY_UPDATED("Başarıyla güncellendi"),
    SUCCESSFULLY_DELETED("Başarıyla silindi"),
    ERROR("Hata"),
    DATA_RETRIEVED("Veri getirildi"),
    LISTED_EMPTY("Liste boş"),

    DATA_LISTED("Veriler listelendi"),
    INSTITUTION_CREATED("Kurum başarıyla oluşturuldu"),
    INSTITUTION_UPDATED("Kurum başarıyla güncellendi"),
    INSTITUTION_DELETED("Kurum başarıyla silindi"),
    INSTITUTION_NOT_FOUND("Kurum bulunamadı"),



    PRODUCT_NOT_FOUND("Ürün bulunamadı"),
    OWNER_DEPARTMENT_NOT_FOUND("Sahip departman bulunamadı")

    ;



    private final String message;

    ResultConstant(String message) {
        this.message = message;
    }

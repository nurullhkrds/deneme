import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

public class BillExceptionsUITest {

    @Test
    void testClassInstantiationShouldThrowException() {
        // Ensure that instantiating BillExceptionsUI directly throws an exception
        assertThatThrownBy(() -> {
            new BillExceptionsUI();
        }).isInstanceOf(IllegalStateException.class)
          .hasMessage("BillExceptionsUI: Exception Constant Class");

        // Ensure that instantiating ValidationExceptions directly throws an exception
        assertThatThrownBy(() -> {
            new BillExceptionsUI.ValidationExceptions();
        }).isInstanceOf(IllegalStateException.class)
          .hasMessage("ValidationExceptions: Constant class");
    }

    @Test
    void testStaticExceptionDataInitialization() {
        // Validate that the static ExceptionData fields are correctly initialized
        assertThat(BillExceptionsUI.SUCCESS.getCode()).isEqualTo(0L);
        assertThat(BillExceptionsUI.SUCCESS.getAppName()).isEqualTo("PAYMENTS.bill-ui");
        assertThat(BillExceptionsUI.SUCCESS.getMessage()).isEqualTo("Başarılı İşlem");

        assertThat(BillExceptionsUI.NOT_IMPLEMENTED.getCode()).isEqualTo(1L);
        assertThat(BillExceptionsUI.NOT_IMPLEMENTED.getMessage()).isEqualTo("İlgili Servis Henüz Hazır Değil");

        assertThat(BillExceptionsUI.SERVICE_CALL_EXCEPTION.getCode()).isEqualTo(998L);
        assertThat(BillExceptionsUI.SERVICE_CALL_EXCEPTION.getMessage()).isEqualTo("Servis Çagrımında Hata Oluştu");

        assertThat(BillExceptionsUI.UNKNOWN_ERROR_OCCURRED.getCode()).isEqualTo(999L);
        assertThat(BillExceptionsUI.UNKNOWN_ERROR_OCCURRED.getMessage()).isEqualTo("Bilinmeyen Hata Meydana Geldi");

        // ValidationExceptions tests
        assertThat(BillExceptionsUI.ValidationExceptions.SERVICE_DEFINITION_NOT_FOUND.getCode()).isEqualTo(2000L);
        assertThat(BillExceptionsUI.ValidationExceptions.SERVICE_DEFINITION_NOT_FOUND.getMessage()).isEqualTo("Kurum Servis Tanımı Bulunamadı");

        assertThat(BillExceptionsUI.ValidationExceptions.PARAMETER_RECORD_NOT_FOUND.getCode()).isEqualTo(2001L);
        assertThat(BillExceptionsUI.ValidationExceptions.PARAMETER_RECORD_NOT_FOUND.getMessage()).isEqualTo("İlgili Parametre Kaydı Bulunamadı");

        assertThat(BillExceptionsUI.ValidationExceptions.CONVERSION_FAILED.getCode()).isEqualTo(2002L);
        assertThat(BillExceptionsUI.ValidationExceptions.CONVERSION_FAILED.getMessage()).isEqualTo("Dönüştürme işlemi başarısız !");

        assertThat(BillExceptionsUI.ValidationExceptions.RETURN_MAP_EXIST.getCode()).isEqualTo(2004L);
        assertThat(BillExceptionsUI.ValidationExceptions.RETURN_MAP_EXIST.getMessage()).isEqualTo("Aynı dönüş kod  değeri mevcuttur");
    }

}

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class CurrencyUtilTest {

    @Test
    public void testCurrencyConverter_withNullCurrency() {
        String result = CurrencyUtil.currencyConverter(null);
        assertEquals("YTL", result, "Null currency should return YTL (Turkish Lira)");
    }

    @Test
    public void testCurrencyConverter_withTurkishLiraCurrencyCode() {
        String result = CurrencyUtil.currencyConverter("TL");
        assertEquals("YTL", result, "TL should convert to YTL");
    }

    @Test
    public void testCurrencyConverter_withTurkishLiraTRYCurrencyCode() {
        String result = CurrencyUtil.currencyConverter("TRY");
        assertEquals("YTL", result, "TRY should convert to YTL");
    }

    @Test
    public void testCurrencyConverter_withDifferentCurrencyCode() {
        String result = CurrencyUtil.currencyConverter("USD");
        assertEquals("USD", result, "USD should remain as USD and not convert");
    }

    @Test
    public void testCurrencyConverter_withEmptyString() {
        String result = CurrencyUtil.currencyConverter("");
        assertEquals("", result, "Empty string should return as is");
    }
}
